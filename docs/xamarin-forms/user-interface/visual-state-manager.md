---
title: Диспетчер визуальных состояний Xamarin.Forms
description: Используйте Диспетчер визуальных состояний вносить изменения в элементы XAML, визуальные состояния набора из кода.
ms.prod: xamarin
ms.assetid: 17296F14-640D-484B-A24C-A4E9B7013E4F
ms.technology: xamarin-forms
ms.custom: xamu-video
author: charlespetzold
ms.author: chape
ms.date: 05/07/2018
ms.openlocfilehash: 0fdcbd6467547647089b436a894b1bc490ba5ee1
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854822"
---
# <a name="the-xamarinforms-visual-state-manager"></a>Диспетчер визуальных состояний Xamarin.Forms

_Используйте Диспетчер визуальных состояний вносить изменения в элементы XAML, визуальные состояния набора из кода._

Visual State Manager (VSM) впервые появилось в Xamarin.Forms 3.0. VSM предоставляет структурированный способ вносить визуальные изменения в пользовательском интерфейсе из кода. В большинстве случаев в пользовательском интерфейсе приложения определяется в XAML, и этот XAML содержит разметку, описывающий влияние Диспетчер визуальных состояний визуальные элементы пользовательского интерфейса.

VSM введено понятие _визуальные состояния_. Представление Xamarin.Forms, такие как `Button` может иметь несколько различное визуальное представление в зависимости от его базового состояния &mdash; ли он отключен, нажатии или имеет фокус ввода. Это состояний кнопки.

Визуальные состояния, собираются в _визуальное состояние группы_. Все визуальные состояния в группе визуальных состояний являются взаимоисключающими. Визуальные состояния и группы визуальных состояний идентифицируются по простые текстовые строки.

Диспетчер визуальных состояний Xamarin.Forms определяет одну группу визуальных состояний с именем «CommonStates» с тремя состояниями visual:

- "Normal"
- «Отключено»
- «С фокусом ввода»

Эта группа визуального состояния поддерживается для всех классов, производных от [ `VisualElement` ](xref:Xamarin.Forms.VisualElement), который является базовым классом для [ `View` ](xref:Xamarin.Forms.View) и [ `Page` ](xref:Xamarin.Forms.Page). 

Можно также определить собственные группы визуальных состояний и визуальные состояния, как в этой статье мы рассмотрим.

> [!NOTE]
> Xamarin.Forms разработчики, знакомые с [триггеры](~/xamarin-forms/app-fundamentals/triggers.md) известно, что триггеры можно также вносить изменения визуальных элементов в пользовательском интерфейсе на основе изменений в свойства представления или обработки событий. Тем не менее использование триггеров для работы с различными сочетаниями этих изменений может стать довольно запутанным. Исторически сложилось так, что Диспетчер визуальных состояний был введен в среду на основе XAML Windows для устранения путаницы, полученный в результате сочетания визуальных состояний. В VSM визуальные состояния в группе визуальных состояний всегда являются взаимоисключающими. В любое время только одно состояние в каждой группе является текущим состоянием.

## <a name="the-common-states"></a>Общие состояния

Диспетчер визуальных состояний позволяет включать в себя разделы в файле XAML, можно изменить внешний вид представления, если представление обычного или отключен или имеет фокус ввода. Они известны как _общих состояний_.

Предположим, что у вас есть `Entry` представление на странице, и нужно, чтобы внешний вид `Entry` для изменения одним из следующих способов:

- `Entry` Должны иметь розовый цвет фона при `Entry` отключена.
- `Entry` Должен иметь фон травяной обычно.
- `Entry` Должен расширяться дважды высоте normal, когда фокус ввода.

Можно подключить VSM разметки для отдельного представления, или если он применяется к нескольким представлениям можно определить в стиле. В следующих двух разделах описываются эти подходы.

### <a name="vsm-markup-on-a-view"></a>Этот режим разметки для представления

Для присоединения VSM разметку для `Entry` просмотра, сначала разделите `Entry` в открывающий и закрывающий теги:

```xaml
<Entry FontSize="18">

</Entry>
```

Она получает размер шрифта явные, так как одно из состояний будет использовать `FontSize` свойство размера текста в `Entry`.

Далее установите `VisualStateManager.VisualStateGroups` между этими тегами:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>

    </VisualStateManager.VisualStateGroups>
</Entry>
```

[`VisualStateGroups`](xref:Xamarin.Forms.VisualStateManager.VisualStateGroupsProperty) является присоединенным свойством привязки определяется [ `VisualStateManager` ](xref:Xamarin.Forms.VisualStateManager) класса. (Дополнительные сведения о присоединенных свойств привязки см. в статье [присоединенных свойств](~/xamarin-forms/xaml/attached-properties.md).) Это как `VisualStateGroups` свойство присоединено к `Entry` объекта.

`VisualStateGroups` Свойство имеет тип [ `VisualStateGroupList` ](xref:Xamarin.Forms.VisualStateGroupList), который является коллекцией [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) объектов. В рамках `VisualStateManager.VisualStateGroups` теги, вставить пару `VisualStateGroup` тегов для каждой группы визуальных состояний, которые требуется включить:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Обратите внимание, что `VisualStateGroup` тег имеет `x:Name` атрибут, указывающий, имя группы. `VisualStateGroup` Класс определяет `Name` свойство, которое можно использовать вместо:

```xaml
<VisualStateGroup Name="CommonStates">
```

Можно использовать либо `x:Name` или `Name` , но не одновременно в одном элементе.

`VisualStateGroup` Класс определяет свойство, именуемое [ `States` ](xref:Xamarin.Forms.VisualStateGroup.States), который является коллекцией [ `VisualState` ](xref:Xamarin.Forms.VisualState) объектов. `States` — _свойства содержимого_ из `VisualStateGroups` поэтому вы можете включить `VisualState` теги непосредственно между `VisualStateGroup` теги. (Содержимого в этой статье описаны свойства [основному синтаксису XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md#content-properties).)

Следующим шагом является включение пару тегов для каждого визуального состояния в этой группе. Их также можно определить с помощью `x:Name` или `Name`:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">

            </VisualState>

            <VisualState x:Name="Focused">

            </VisualState>

            <VisualState x:Name="Disabled">

            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

`VisualState` Определяет свойство, именуемое [ `Setters` ](xref:Xamarin.Forms.VisualState.Setters), который является коллекцией [ `Setter` ](xref:Xamarin.Forms.Setter) объектов. Это же `Setter` объекты, используемые в [ `Style` ](xref:Xamarin.Forms.Style) объекта.

`Setters` — _не_ свойство content `VisualState`, поэтому необходимо включить теги элемента свойства для `Setters` свойства:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
    
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>

                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Теперь можно вставить один или несколько `Setter` объектов между каждой парой `Setters` теги. Это `Setter` объекты, определяющие визуальные состояния, описанные ранее:

```xaml
<Entry FontSize="18">
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Lime" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Focused">
                <VisualState.Setters>
                    <Setter Property="FontSize" Value="36" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Disabled">
                <VisualState.Setters>
                    <Setter Property="BackgroundColor" Value="Pink" />
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Entry>
```

Каждый `Setter` тег указывает значение конкретного свойства, при это состояние является текущей. Любое свойство ссылается `Setter` объект должен быть подкреплен может быть привязано.

Разметку, аналогичную следующей является основой **VSM для представления** странице в **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** пример программы. Страницы включает в себя три `Entry` представления, но только второй имеет разметки VSM, подключенные к ней:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:VsmDemos"
             x:Class="VsmDemos.MainPage"
             Title="VSM Demos">

    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />

        <Label Text="Entry with VSM: " />

        <Entry>
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup x:Name="CommonStates">
                    
                    <VisualState x:Name="Normal">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Lime" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Focused">
                        <VisualState.Setters>
                            <Setter Property="FontSize" Value="36" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState x:Name="Disabled">
                        <VisualState.Setters>
                            <Setter Property="BackgroundColor" Value="Pink" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>

            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Обратите внимание, что второй `Entry` также имеет `DataTrigger` как часть его `Trigger` коллекции. В результате `Entry` требуется отключить, пока что-то вводится в третий `Entry`. Ниже приведен страницы при запуске под управлением iOS, Android и универсальной платформы Windows (UWP).

[![Этот режим для представления: отключена](vsm-images/VsmOnViewDisabled.png "VSM на вид - отключен")](vsm-images/VsmOnViewDisabled-Large.png#lightbox)

Текущее визуальное состояние «Отключено» поэтому фона второго `Entry` имеет розовый на iOS и Android экранов. Реализация UWP `Entry` не разрешает Задание фона цвет при `Entry` отключена. 

При вводе какой-либо текст в третий `Entry`, второй `Entry` коммутаторы в состояние «Normal», а фон стал травяной:

[![Этот режим для представления: обычный](vsm-images/VsmOnViewNormal.png "VSM на представление — обычный")](vsm-images/VsmOnViewNormal-Large.png#lightbox)

Когда touch второй `Entry`, он получает фокус ввода. Он переключается в состояние «Focused» и дважды его высота при развертывании:

[![Этот режим для представления: с фокусом ввода](vsm-images/VsmOnViewFocused.png "VSM на представление — с фокусом ввода")](vsm-images/VsmOnViewFocused-Large.png#lightbox)

Обратите внимание, что `Entry` не содержит фона травяной, когда он получает фокус ввода. Как Диспетчер визуальных состояний переключается между визуальными состояниями, свойств, заданных в предыдущее состояние не задано. Имейте в виду, что визуальные состояния являются взаимоисключающими. «Normal» состояние означает, только что `Entry` включен. Это означает, что `Entry` включен и имеет фокус ввода. 

Если вы хотите, чтобы `Entry` требуется травяной фона в состоянии «Фокуса», добавьте еще один `Setter` к этому состоянию visual:

```xaml
<VisualState x:Name="Focused">
    <VisualState.Setters>
        <Setter Property="FontSize" Value="36" />
        <Setter Property="BackgroundColor" Value="Lime" />
    </VisualState.Setters>
</VisualState>
```

В порядке, для них `Setter` объектов для правильной работы `VisualStateGroup` должен содержать `VisualState` объекты для всех состояний в эту группу. При наличии визуальное состояние, не имеющей `Setter` объектов, включить его в любом случае как пустой тег:

```xaml
<VisualState x:Name="Normal" />
``` 

### <a name="visual-state-manager-markup-in-a-style"></a>Разметка Диспетчер визуальных состояний в стиле

Часто бывает необходимо совместно использовать одну разметку Диспетчер визуальных состояний между два или несколько представлений. В этом случае нужно будет поместить в разметке `Style` определения.

Здесь подразумевается существующий `Style` для `Entry` элементов в **VSM на представление** страницы:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
</Style> 
```

Добавить `Setter` теги для `VisualStateManager.VisualStateGroups` присоединенного привязываемые свойства:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">

    </Setter>
</Style> 
```

Свойства содержимого для `Setter` — `Value`, поэтому значение `Value` свойство может быть указано непосредственно в эти теги. Свойство имеет тип `VisualStateGroupList`:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>

        </VisualStateGroupList>
    </Setter>
</Style> 
```

В этих тегах можно включить один или несколько `VisualStateGroup` объектов:

```xaml
<Style TargetType="Entry">
    <Setter Property="Margin" Value="20, 0" />
    <Setter Property="FontSize" Value="18" />
    <Setter Property="VisualStateManager.VisualStateGroups">
        <VisualStateGroupList>
            <VisualStateGroup x:Name="CommonStates">

            </VisualStateGroup>
        </VisualStateGroupList>
    </Setter>
</Style> 
```

Остальная часть разметки VSM используется теми же самыми.

Вот **VSM в стиле** страница завершения VSM разметки:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmInStylePage"
             Title="VSM in Style">
    <StackLayout>
        <StackLayout.Resources>
            <Style TargetType="Entry">
                <Setter Property="Margin" Value="20, 0" />
                <Setter Property="FontSize" Value="18" />
                <Setter Property="VisualStateManager.VisualStateGroups">
                    <VisualStateGroupList>
                        <VisualStateGroup x:Name="CommonStates">

                            <VisualState x:Name="Normal">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Focused">
                                <VisualState.Setters>
                                    <Setter Property="FontSize" Value="36" />
                                    <Setter Property="BackgroundColor" Value="Lime" />
                                </VisualState.Setters>
                            </VisualState>

                            <VisualState x:Name="Disabled">
                                <VisualState.Setters>
                                    <Setter Property="BackgroundColor" Value="Pink" />
                                </VisualState.Setters>
                            </VisualState>
                        </VisualStateGroup>
                    </VisualStateGroupList>
                </Setter>
            </Style>

            <Style TargetType="Label">
                <Setter Property="Margin" Value="20, 30, 20, 0" />
                <Setter Property="FontSize" Value="Large" />
            </Style>
        </StackLayout.Resources>

        <Label Text="Normal Entry:" />

        <Entry />
        
        <Label Text="Entry with VSM: " />

        <Entry>
            <Entry.Triggers>
                <DataTrigger TargetType="Entry"
                             Binding="{Binding Source={x:Reference entry3},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Entry.Triggers>
        </Entry>

        <Label Text="Entry to enable 2nd Entry:" />

        <Entry x:Name="entry3"
               Text=""
               Placeholder="Type something to enable 2nd Entry" />
    </StackLayout>
</ContentPage>
```

Теперь все `Entry` одинаково для их визуальные состояния, реагировать на представления на этой странице. Обратите внимание, что в состоянии «Фокуса» теперь включает секунды `Setter` , обеспечивающая каждого `Entry` травяной фоновых также, если он имеет фокус ввода:

[![Этот режим в стиле](vsm-images/VsmInStyle.png "VSM в стиле")](vsm-images/VsmInStyle-Large.png#lightbox)

## <a name="defining-your-own-visual-states"></a>Определение собственных визуальных состояний

Каждый класс, производный от `VisualElement` поддерживает три общих состояний «Normal», «С фокусом ввода» и «Отключено». На внутреннем уровне [ `VisualElement` ](https://github.com/xamarin/Xamarin.Forms/blob/master/Xamarin.Forms.Core/VisualElement.cs) класс обнаруживает, когда он становится включено или отключено, или фокус ввода и терять и вызывает статический [ `VisualStateManager.GoToState` ](xref:Xamarin.Forms.VisualStateManager.GoToState(Xamarin.Forms.VisualElement,System.String)) метод:

```csharp
VisualStateManager.GoToState(this, "Focused");
```

Это единственный код Диспетчер визуальных состояний, которые находятся в `VisualElement` класса. Так как `GoToState` вызывается для каждого объекта, на основе каждого класса, производный от `VisualElement`, Диспетчер визуальных состояний можно использовать с любым `VisualElement` объект реагировать на эти изменения.

Интересно, что имя группы визуальных состояний «CommonStates» нет явной ссылки в `VisualElement`. Имя группы не является частью API-Интерфейс для диспетчера визуальных состояний. В одном из двух пример программы, приведенные выше, можно изменить имя группы из «CommonStates», на что-нибудь еще, и программа будет по-прежнему работать. Имя группы является просто общим описанием состояния из этой группы. Неявно понятно, что визуальные состояния в любой группе являются взаимоисключающими: одно состояние и только одно состояние является текущей в любое время.

Если вы хотите реализовать свои собственные визуальные состояния, необходимо вызвать `VisualStateManager.GoToState` из кода. Чаще всего будет вызывать этот метод из файла кода класса страницы.

**Проверки VSM** странице в **[VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)** примере показано, как использовать Диспетчер визуальных состояний, в связи с проверки входных данных. Файл XAML состоит из двух `Label` элементов, `Entry`, и `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmValidationPage"
             Title="VSM Validation">
    <StackLayout Padding="10, 10">
        
        <Label Text="Enter a U.S. phone number:"
               FontSize="Large" />

        <Entry Placeholder="555-555-5555"
               FontSize="Large"
               Margin="30, 0, 0, 0"
               TextChanged="OnTextChanged" />

        <Label x:Name="helpLabel"
               Text="Phone number must be of the form 555-555-5555, and not begin with a 0 or 1">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid">
                        <VisualState.Setters>
                            <Setter Property="TextColor" Value="Transparent" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Invalid" />
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Label>

        <Button x:Name="submitButton"
                Text="Submit"
                FontSize="Large"
                Margin="0, 20"
                VerticalOptions="Center"
                HorizontalOptions="Center">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="ValidityStates">
                    
                    <VisualState Name="Valid" />

                    <VisualState Name="Invalid">
                        <VisualState.Setters>
                            <Setter Property="IsEnabled" Value="False" />
                        </VisualState.Setters>
                    </VisualState>
                    
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
        </Button>
    </StackLayout>
</ContentPage>
```

VSM разметки подключен ко второму `Label` (с именем `helpLabel`) и `Button` (с именем `submitButton`). Существуют два состояния взаимоисключающими, с именем «Допустимо» и «Недопустимый». Обратите внимание на то, что каждая из двух групп «ValidationState» содержит `VisualState` теги для «Допустимо» и «Недопустимый», несмотря на то, что один из них является пустым в каждом конкретном случае. 

Если `Entry` не содержит допустимый номер телефона, то текущее состояние — «Недопустимо» и поэтому вторая `Label` является видимым и `Button` отключен:

[![Проверка VSM: Недопустимое состояние](vsm-images/VsmValidationInvalid.png "VSM проверки — недопустимый")](vsm-images/VsmValidationInvalid-Large.png#lightbox)

Если введен правильный номер телефона, текущее состояние становится «Действителен». Второй `Entry` исчезает и `Button` включена:

[![Проверка VSM: Недопустимое состояние](vsm-images/VsmValidationValid.png "проверка VSM - допустимым")](vsm-images/VsmValidationValid-Large.png#lightbox)

Файл с выделенным кодом — определяет, какое обработки `TextChanged` событий из `Entry`. Обработчик использует регулярное выражение для определения, если входная строка является допустимым, или нет. Метод в файле кода программной части с именем `GoToState` вызывает статический `VisualStateManager.GoToState` метод для обоих `helpLabel` и `submitButton`:

```csharp
public partial class VsmValidationPage : ContentPage
{
    public VsmValidationPage ()
    {
        InitializeComponent ();

        GoToState(false);
    }

    void OnTextChanged(object sender, TextChangedEventArgs args)
    {
        bool isValid = Regex.IsMatch(args.NewTextValue, @"^[2-9]\d{2}-\d{3}-\d{4}$");
        GoToState(isValid);
    }

    void GoToState(bool isValid)
    {
        string visualState = isValid ? "Valid" : "Invalid";
        VisualStateManager.GoToState(helpLabel, visualState);
        VisualStateManager.GoToState(submitButton, visualState);
    }
}
```

Обратите внимание, что `GoToState` метод вызывается из конструктора для инициализации состояния. Всегда должно быть текущее состояние. Но ничто в коде не существует любая ссылка на имя группы визуальных состояний, несмотря на то, что он указывается в XAML как «ValidationStates» в целях ясности. 

Обратите внимание на то, что файл с выделенным кодом должны учитываться каждого объекта на странице, зависит, эти визуальные состояния, а также вызывать `VisualStateManager.GoToState` для каждого из этих объектов. В этом примере это только два объекта ( `Label` и `Button`), но это может быть несколько больше.

Может возникнуть вопрос: Если файл с выделенным кодом необходимо ссылаться на каждый объект, на странице, зависит от этих визуальных состояний, почему файл с выделенным кодом напрямую недоступно для просто объекты? Конечно же удалось. Однако преимущество использования VSM — возможности контролировать то, как визуальные элементы реагировать на другое состояние полностью в XAML, который хранит все Дизайн пользовательского интерфейса в одном месте. Это позволяет избежать параметр внешний вид, обратившись к визуальные элементы непосредственно из кода.

Было бы интересно, которые следует учитывать класс, производный от `Entry` и возможно определение свойства, которое можно задать в функцию внешней проверки. Класс, производный от `Entry` затем можно вызвать `VisualStateManager.GoToState` метод. Эта схема будет корректно работать, но только в том случае, если `Entry` были только объекта, связанного с различных визуальных состояний. В этом примере `Label` и `Button` также будет изменяться. Не существует способа VSM разметки, присоединенных к `Entry` для управления других объектов на странице и нет способа VSM разметки, присоединенных к этим других объектов для ссылки на изменение визуального состояния из другого объекта.

<a name="adaptive-layout" />

## <a name="using-the-visual-state-manager-for-adaptive-layout"></a>С помощью диспетчера визуальных состояний для адаптивной макета

Xamarin.Forms, которое обычно приложения, работающего на телефоне можно просмотреть в книжной или Альбомная пропорции и Xamarin.Forms, выполняющихся на рабочем столе можно изменять принимать множество различных размеров и пропорций. Даже хорошо проработанное приложение может отображать его содержимое по-разному для этих различных странице или в окне форм-факторов. 

Этот прием называется иногда _адаптивный макет_. Поскольку адаптивный макет исключительно включает визуальные элементы программы, это приложение идеально Диспетчер визуальных состояний.

Простым примером является приложение, отображающее это небольшая совокупность кнопок, которые влияют на содержимое приложения. В книжной ориентации эти кнопки могут отображаться в верхней части страницы по горизонтали:

[![VSM адаптивный макет: Книжная](vsm-images/VsmAdaptiveLayoutPortrait.png "VSM адаптивный макет — книжной ориентации")](vsm-images/VsmAdaptiveLayoutPortrait-Large.png#lightbox)

В альбомной ориентации массив кнопки может быть перемещен к одной стороне и отображаемому в столбце:

[![Адаптивный макет VSM: В альбомной ориентации](vsm-images/VsmAdaptiveLayoutLandscape.png "VSM адаптивный макет — альбомной ориентации")](vsm-images/VsmAdaptiveLayoutLandscape-Large.png#lightbox)

Сверху вниз программа выполняется в универсальной платформы Windows, Android и iOS.

**Адаптивный макет VSM** странице в [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/) образец определяет группу с именем «OrientationStates» с двумя визуальными состояниями с именем «» и «Книжной ориентации». (Более сложный подход может основываться на несколько различных значений ширины страницы или окно.) 

Этот режим разметки происходит в четырех местах в файле XAML. `StackLayout` С именем `mainStack` содержит меню и содержимое, которое является `Image` элемент. Это `StackLayout` должны иметь вертикальную ориентацию в книжной ориентации и горизонтальной ориентации в альбомном режиме:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="VsmDemos.VsmAdaptiveLayoutPage"
             Title="VSM Adaptive Layout">

    <StackLayout x:Name="mainStack">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup Name="OrientationStates">

                <VisualState Name="Portrait">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Vertical" />
                    </VisualState.Setters>
                </VisualState>

                <VisualState Name="Landscape">
                    <VisualState.Setters>
                        <Setter Property="Orientation" Value="Horizontal" />
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>
        
        <ScrollView x:Name="menuScroll">
            <VisualStateManager.VisualStateGroups>
                <VisualStateGroup Name="OrientationStates">
                    
                    <VisualState Name="Portrait">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Horizontal" />
                        </VisualState.Setters>
                    </VisualState>

                    <VisualState Name="Landscape">
                        <VisualState.Setters>
                            <Setter Property="Orientation" Value="Vertical" />
                        </VisualState.Setters>
                    </VisualState>
                </VisualStateGroup>
            </VisualStateManager.VisualStateGroups>
            
            <StackLayout x:Name="menuStack">
                <VisualStateManager.VisualStateGroups>
                    <VisualStateGroup Name="OrientationStates">

                        <VisualState Name="Portrait">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Horizontal" />
                            </VisualState.Setters>
                        </VisualState>

                        <VisualState Name="Landscape">
                            <VisualState.Setters>
                                <Setter Property="Orientation" Value="Vertical" />
                            </VisualState.Setters>
                        </VisualState>
                    </VisualStateGroup>
                </VisualStateManager.VisualStateGroups>

                <StackLayout.Resources>
                    <Style TargetType="Button">
                        <Setter Property="VisualStateManager.VisualStateGroups">
                            <VisualStateGroupList>
                                <VisualStateGroup Name="OrientationStates">

                                    <VisualState Name="Portrait">
                                        <VisualState.Setters>
                                            <Setter Property="HorizontalOptions" Value="CenterAndExpand" />
                                            <Setter Property="Margin" Value="10, 5" />
                                        </VisualState.Setters>
                                    </VisualState>

                                    <VisualState Name="Landscape">
                                        <VisualState.Setters>
                                            <Setter Property="VerticalOptions" Value="CenterAndExpand" />
                                            <Setter Property="HorizontalOptions" Value="Center" />
                                            <Setter Property="Margin" Value="10" />
                                        </VisualState.Setters>
                                    </VisualState>
                                </VisualStateGroup>
                            </VisualStateGroupList>
                        </Setter>
                    </Style>
                </StackLayout.Resources>

                <Button Text="Banana"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="Banana.jpg" />
                
                <Button Text="Face Palm"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="FacePalm.jpg" />
                
                <Button Text="Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="monkey.png" />
                
                <Button Text="Seated Monkey"
                        Command="{Binding SelectedCommand}"
                        CommandParameter="SeatedMonkey.jpg" />
            </StackLayout>
        </ScrollView>

        <Image x:Name="image"
               VerticalOptions="FillAndExpand"
               HorizontalOptions="FillAndExpand" />
    </StackLayout>
</ContentPage>
```

Внутренний `ScrollView` с именем `menuScroll` и `StackLayout` с именем `menuStack` реализации меню кнопок. Ориентацию макетов является противоположностью из `mainStack`. Меню должно быть по горизонтали в книжной ориентации и по вертикали в альбомной ориентации.

В четвертой части разметки VSM находится в неявный стиль кнопок сами. Эта разметка задает `VerticalOptions`, `HorizontalOptions`, и `Margin` свойства, относящиеся к portait и альбомной ориентации.

Задает файл кода программной `BindingContext` свойство `menuStack` для реализации `Button` системе команд, а также присоединяет обработчик к `SizeChanged` событий страницы:

```csharp
public partial class VsmAdaptiveLayoutPage : ContentPage
{
    public VsmAdaptiveLayoutPage ()
    {
        InitializeComponent ();

        SizeChanged += (sender, args) =>
        {
            string visualState = Width > Height ? "Landscape" : "Portrait";
            VisualStateManager.GoToState(mainStack, visualState);
            VisualStateManager.GoToState(menuScroll, visualState);
            VisualStateManager.GoToState(menuStack, visualState);

            foreach (View child in menuStack.Children)
            {
                VisualStateManager.GoToState(child, visualState);
            }
        };

        SelectedCommand = new Command<string>((filename) =>
        {
            image.Source = ImageSource.FromResource("VsmDemos.Images." + filename);
        });

        menuStack.BindingContext = this;
    }

    public ICommand SelectedCommand { private set; get; }
}
```

`SizeChanged` Вызовов обработчика `VisualStateManager.GoToState` для двух `StackLayout` и `ScrollView` элементы и затем перебирает дочерних элементов `menuStack` для вызова `VisualStateManager.GoToState` для `Button` элементов.

Может показаться, как если бы файл с выделенным кодом может обрабатывать изменения ориентации более напрямую, задав свойства элементов в файле XAML, но Диспетчер визуальных состояний определенно более структурированный подход. Все, что визуальные элементы хранятся в файле XAML, где они становятся проще для изучения, обслуживании и изменении.

## <a name="visual-state-manager-with-xamarinuniversity"></a>Диспетчер визуальных состояний с Xamarin.University

> [!VIDEO https://youtube.com/embed/qhUHbVP5mIQ]

**Диспетчер визуальных состояний Xamarin.Forms 3.0, а по [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Связанные ссылки

- [VsmDemos](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/VsmDemos/)

