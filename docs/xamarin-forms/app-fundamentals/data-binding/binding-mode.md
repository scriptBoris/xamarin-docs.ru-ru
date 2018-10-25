---
title: Режим привязки Xamarin.Forms
description: В этой статье объясняется, как для управления потоком данных между источником и целью режиме привязки, который указывается с помощью члена перечисления BindingMode. Свойство, используемое каждый элемент имеет режим привязки по умолчанию, который указывает режим, по сути, когда это свойство является целевым объектом привязки данных.
ms.prod: xamarin
ms.assetid: D087C389-2E9E-47B9-A341-5B14AC732C45
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 05/01/2018
ms.openlocfilehash: 420c1de0691de419180dd497a9031ea5e7dd1054
ms.sourcegitcommit: 7f6127c2f425fadc675b77d14de7a36103cff675
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "37935671"
---
# <a name="xamarinforms-binding-mode"></a>Режим привязки Xamarin.Forms

В [предыдущей статье](basic-bindings.md), **привязки кода вместо** и **привязки XAML альтернатива** избранные страницы `Label` с его `Scale` свойство привязан к `Value` свойство `Slider`. Так как `Slider` начальное значение равно 0, это вызвано `Scale` свойство `Label` равным 0, а не 1 и `Label` исчез.

В [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) образец, **обратный привязки** страница аналогична программ в предыдущей статье, за исключением того, что привязка данных определяется на `Slider` , а не на `Label`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DataBindingDemos.ReverseBindingPage"
             Title="Reverse Binding">
    <StackLayout Padding="10, 0">

        <Label x:Name="label"
               Text="TEXT"
               FontSize="80"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />

        <Slider x:Name="slider"
                VerticalOptions="CenterAndExpand"
                Value="{Binding Source={x:Reference label},
                                Path=Opacity}" />
    </StackLayout>
</ContentPage>
```

Поначалу это может показаться обратной: теперь `Label` является источником привязки данных и `Slider` является целевым объектом. Ссылки привязки `Opacity` свойство `Label`, который имеет значение по умолчанию 1.

Как можно догадаться, `Slider` инициализируется со значением 1 в первой `Opacity` значение `Label`. Это показано на снимке экрана iOS в левой части:

[![Отменить привязку](binding-mode-images/reversebinding-small.png "обратный привязки")](binding-mode-images/reversebinding-large.png#lightbox "обратный привязки")

Вы будете удивлены, `Slider` продолжает работать, как показано на снимках экрана Android и UWP. Это первый взгляд, что привязка данных работает лучше, когда `Slider` является целевым объектом привязки, а не `Label` так, как инициализация работать в обычном режиме может ожидать.

Разница между **обратный привязки** пример и более ранних примеры включает в себя *режим привязки*.

## <a name="the-default-binding-mode"></a>Режим привязки по умолчанию

Режим привязки указывается с помощью члена [ `BindingMode` ](xref:Xamarin.Forms.BindingMode) перечисления:

- [`Default`](xref:Xamarin.Forms.BindingMode.Default)
- [`TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) &ndash; обе стороны входящих данных между источником и целью
- [`OneWay`](xref:Xamarin.Forms.BindingMode.OneWay) &ndash; входящих данных от источника к целевому объекту
- [`OneWayToSource`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; данные передаются от целевого объекта к источнику
- [`OneTime`](xref:Xamarin.Forms.BindingMode.OneWayToSource) &ndash; данные отправляются из источника в целевой объект, но только если `BindingContext` изменения (новая с Xamarin.Forms 3.0)

Каждое свойство, привязываемых имеет режим привязки, который задается при создании свойство, используемое по умолчанию, и которая доступна из [ `DefaultBindingMode` ](xref:Xamarin.Forms.BindableProperty.DefaultBindingMode) свойство `BindableProperty` объекта. Этот режим по умолчанию фактически указывает режим, когда это свойство является целевым объектом привязки данных.

Режим привязки по умолчанию для большинства свойств, таких как `Rotation`, `Scale`, и `Opacity` является `OneWay`. Если эти свойства цели привязки данных, то целевое свойство устанавливается из источника.

Тем не менее режим привязки по умолчанию для `Value` свойство `Slider` является `TwoWay`. Это означает, что при `Value` свойство является целевым объектом привязки данных, а затем целевой объект имеет значение из источника (как обычно), но источник также имеет значение от целевого объекта. Это позволяет `Slider` из первоначального `Opacity` значение.

Может показаться этот двухстороннюю привязку создают бесконечный цикл, но этого не произойдет. Привязываемые свойства не сигнал изменение свойства, если свойство изменяется. Это позволяет предотвратить бесконечный цикл.

### <a name="two-way-bindings"></a>Имеют двухсторонние привязки

Наиболее привязываемые свойства использовать режим привязки по умолчанию `OneWay` режим привязки по умолчанию, но следующие свойства `TwoWay`:

- `Date` Свойство `DatePicker`
- `Text` Свойство `Editor`, `Entry`, `SearchBar`, и `EntryCell`
- `IsRefreshing` Свойство `ListView`
- `SelectedItem` Свойство `MultiPage`
- `SelectedIndex` и `SelectedItem` свойства `Picker`
- `Value` Свойство `Slider` и `Stepper`
- `IsToggled` Свойство `Switch`
- `On` Свойство `SwitchCell`
- `Time` Свойство `TimePicker`

Эти конкретные свойства определяются как `TwoWay` для веская причина:

При использовании привязки данных с помощью архитектуры Model-View-ViewModel (MVVM) приложения класс ViewModel является источник привязки данных и представление, которое состоит из представления, такие как `Slider`, являющихся целевыми объектами для привязки данных. Привязки MVVM напоминают **обратная привязка** пример больше, чем привязок в предыдущих примерах. Это очень вероятно, что требуется, чтобы в каждом представлении на странице, чтобы инициализировать значением соответствующего свойства в ViewModel, но изменения в представлении также влияет на свойство модели представления.

Свойства с помощью режима привязки по умолчанию `TwoWay` являются те свойства, скорее всего, для использования в сценариях MVVM.

### <a name="one-way-to-source-bindings"></a>Один-односторонним к источнику привязки

Только для чтения свойства связывания использовать режим привязки по умолчанию `OneWayToSource`. Имеется только один чтения и записи свойство, используемое с режимом привязки по умолчанию `OneWayToSource`:

- `SelectedItem` Свойство `ListView`

Основной причиной является то, что привязки на `SelectedItem` свойство должно привести параметр источника привязки. Пример далее в этой статье переопределяет это поведение.

### <a name="one-time-bindings"></a>Одноразовый привязки

Режим привязки по умолчанию имеют несколько свойств `OneTime`. Эти особые значения приведены ниже.

- `IsTextPredictionEnabled` Свойство `Entry`
- `Text`, `BackgroundColor`, и `Style` свойства `Span`.

Целевые свойства с режимом привязки `OneTime` обновляются только при изменении контекста привязки. Для привязок на эти свойства целевого объекта это упрощает инфраструктура привязки, так как нет необходимости отслеживать изменения в свойствах источника.

## <a name="viewmodels-and-property-change-notifications"></a>ViewModels и уведомления об изменении свойства

**Простой селектор цвета** страницы демонстрируется использование простой модели представления. Данные привязки позволяют пользователю выбрать цвет с использованием трех `Slider` элементы для цветового тона, насыщенности и яркости.

ViewModel является источником привязки данных. ViewModel *не* определить свойства связывания, но он реализует механизм уведомлений, который позволяет инфраструктуре привязки получать уведомления при изменении значения свойства. Этот механизм уведомлений [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) интерфейс, который определяет одно свойство с именем [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged.PropertyChanged). Класс, реализующий этот интерфейс обычно запускает событие при изменении значения одного из своих общих свойств. Событие не инициируется в том случае, если свойство никогда не меняется. ( `INotifyPropertyChanged` Также реализован интерфейс `BindableObject` и `PropertyChanged` событие всегда возникает при изменении значения свойства привязки.)

`HslColorViewModel` Класс определяет пять свойств: `Hue`, `Saturation`, `Luminosity`, и `Color` свойства взаимосвязаны. Если один из трех цветовые компоненты значение изменения `Color` свойство пересчитывается, и `PropertyChanged` события запускаются для всех четырех свойств:

```csharp
public class HslColorViewModel : INotifyPropertyChanged
{
    Color color;
    string name;

    public event PropertyChangedEventHandler PropertyChanged;

    public double Hue
    {
        set
        {
            if (color.Hue != value)
            {
                Color = Color.FromHsla(value, color.Saturation, color.Luminosity);
            }
        }
        get
        {
            return color.Hue;
        }
    }

    public double Saturation
    {
        set
        {
            if (color.Saturation != value)
            {
                Color = Color.FromHsla(color.Hue, value, color.Luminosity);
            }
        }
        get
        {
            return color.Saturation;
        }
    }

    public double Luminosity
    {
        set
        {
            if (color.Luminosity != value)
            {
                Color = Color.FromHsla(color.Hue, color.Saturation, value);
            }
        }
        get
        {
            return color.Luminosity;
        }
    }

    public Color Color
    {
        set
        {
            if (color != value)
            {
                color = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Hue"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Saturation"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Luminosity"));
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Color"));

                Name = NamedColor.GetNearestColorName(color);
            }
        }
        get
        {
            return color;
        }
    }

    public string Name
    {
        private set
        {
            if (name != value)
            {
                name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Name"));
            }
        }
        get
        {
            return name;
        }
    }
}
```

При `Color` изменения свойств, статический `GetNearestColorName` метод в `NamedColor` класса (также входит в **DataBindingDemos** решения) получает ближайшую именованный цвет и задает `Name` свойства. Это `Name` свойство имеет закрытый `set` метод доступа, поэтому его нельзя установить из вне класса.

Если модель представления задано как источник привязки, инфраструктура привязки присоединяет обработчик к `PropertyChanged` событий. Таким образом можно получать уведомления об изменениях свойств привязки и можно затем задать свойства целевого объекта в измененные значения.

Тем не менее если целевое свойство (или `Binding` определения на свойство target) имеет `BindingMode` из `OneTime`, нет необходимости присоединить обработчик на инфраструктура привязки `PropertyChanged` событий. Целевое свойство обновляется только тогда, когда `BindingContext` изменения, а не само свойство источника изменения.

**Простой селектор цвета** файл XAML создает экземпляр `HslColorViewModel` в словаре ресурсов страницы и инициализирует `Color` свойство. `BindingContext` Свойство `Grid` присваивается `StaticResource` привязка расширения для ссылки на этот ресурс:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SimpleColorSelectorPage">

    <ContentPage.Resources>
        <ResourceDictionary>
            <local:HslColorViewModel x:Key="viewModel"
                                     Color="MediumTurquoise" />

            <Style TargetType="Slider">
                <Setter Property="VerticalOptions" Value="CenterAndExpand" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>

    <Grid BindingContext="{StaticResource viewModel}">
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <BoxView Color="{Binding Color}"
                 Grid.Row="0" />

        <StackLayout Grid.Row="1"
                     Margin="10, 0">

            <Label Text="{Binding Name}"
                   HorizontalTextAlignment="Center" />

            <Slider Value="{Binding Hue}" />

            <Slider Value="{Binding Saturation}" />

            <Slider Value="{Binding Luminosity}" />
        </StackLayout>
    </Grid>
</ContentPage>
```

`BoxView`, `Label`И три `Slider` представления наследуют контекст привязки из `Grid`. Эти представления находятся все целевые объекты привязки, которые ссылаются на источник свойства в ViewModel. Для `Color` свойство `BoxView`и `Text` свойство `Label`, привязки данных — это `OneWay`: в представлении свойств в окне свойств в ViewModel.

`Value` Свойство `Slider`, однако `TwoWay`. Это позволит каждой `Slider` задаваемый из ViewModel, а также для ViewModel, устанавливаемое из каждого `Slider`.

При первом запуске программы, `BoxView`, `Label`и три `Slider` элементов имеют значение из модели представления, в зависимости от первоначального `Color` свойство, задайте при создании экземпляра модели представления. Это показано на снимке экрана iOS слева:

[![Средство выбора цвета простого](binding-mode-images/simplecolorselector-small.png "выбора цвета простого")](binding-mode-images/simplecolorselector-large.png#lightbox "простой палитры")

Во время работы ползунки, `BoxView` и `Label` обновляются соответствующим образом, как показано на снимках экрана Android и UWP.

Создание экземпляров ViewModel в словаре ресурсов является распространенным подходом. Можно также создать экземпляр ViewModel в теги элемента свойства для `BindingContext` свойство. В **простой селектор цвета** XAML файл, попробуйте удалить `HslColorViewModel` из словаря ресурсов и присвойте ему значение `BindingContext` свойство `Grid` следующим образом:

```xaml
<Grid>
    <Grid.BindingContext>
        <local:HslColorViewModel Color="MediumTurquoise" />
    </Grid.BindingContext>

    ···

</Grid>
```

Контекст привязки можно задать в различными способами. В некоторых случаях файл с выделенным кодом создает экземпляры ViewModel и заменяет его на `BindingContext` свойства страницы. Ниже приведены все допустимые подходы.

## <a name="overriding-the-binding-mode"></a>Переопределение режим привязки

Если целевое свойство режимом по умолчанию не подходит для привязки данных, можно переопределить, задав [ `Mode` ](xref:Xamarin.Forms.BindingBase.Mode) свойство `Binding` (или [ `Mode` ](xref:Xamarin.Forms.Xaml.BindingExtension.Mode) свойство `Binding` расширение разметки) к одному из членов `BindingMode` перечисления.

Однако можно присвоить `Mode` свойства `TwoWay` не всегда работает, как следует из названия. Например, попробуйте изменить **привязки XAML альтернатива** файл XAML, чтобы включить `TwoWay` в определении привязки:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=TwoWay}" />
```

Возможно, `Slider` инициализировалась в начальное значение `Scale` свойства, равное 1, но этого не произойдет. Когда `TwoWay` инициализируется привязки, целевой объект имеет значение из источника во-первых, означающее, что `Scale` свойству `Slider` значение 0 по умолчанию. Когда `TwoWay` привязка устанавливается для `Slider`, а затем `Slider` первоначально задается из источника.

Можно задать режим привязки `OneWayToSource` в **привязки XAML альтернатива** пример:

```xaml
<Label Text="TEXT"
       FontSize="40"
       HorizontalOptions="Center"
       VerticalOptions="CenterAndExpand"
       Scale="{Binding Source={x:Reference slider},
                       Path=Value,
                       Mode=OneWayToSource}" />
```

Теперь `Slider` устанавливается равным 1 (значение по умолчанию `Scale`) но управление `Slider` не влияет на `Scale` свойство, так что это не очень полезно.

> [!NOTE]
> [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) Класс также определяет [ `ScaleX` ](xref:Xamarin.Forms.VisualElement.ScaleX) и [ `ScaleY` ](xref:Xamarin.Forms.VisualElement.ScaleY) свойства, которые можно масштабировать `VisualElement` по-разному в горизонтальном и вертикальном направлениях.

Очень полезно приложения с режимом по умолчанию, переопределяющий `TwoWay` включает в себя `SelectedItem` свойство `ListView`. Режим привязки по умолчанию является `OneWayToSource`. Если привязка данных имеет значение на `SelectedItem` свойство для ссылки на источник свойства в модели представления, то это свойство источника задается из `ListView` выбора. Однако в некоторых случаях можно также `ListView` инициализированную из ViewModel.

**Примеры параметров** этот метод продемонстрирован страницы. Эта страница представляет простой реализации параметров приложения, которые определены в модели представления, например, это очень часто `SampleSettingsViewModel` файла:

```csharp
public class SampleSettingsViewModel : INotifyPropertyChanged
{
    string name;
    DateTime birthDate;
    bool codesInCSharp;
    double numberOfCopies;
    NamedColor backgroundNamedColor;

    public event PropertyChangedEventHandler PropertyChanged;

    public SampleSettingsViewModel(IDictionary<string, object> dictionary)
    {
        Name = GetDictionaryEntry<string>(dictionary, "Name");
        BirthDate = GetDictionaryEntry(dictionary, "BirthDate", new DateTime(1980, 1, 1));
        CodesInCSharp = GetDictionaryEntry<bool>(dictionary, "CodesInCSharp");
        NumberOfCopies = GetDictionaryEntry(dictionary, "NumberOfCopies", 1.0);
        BackgroundNamedColor = NamedColor.Find(GetDictionaryEntry(dictionary, "BackgroundNamedColor", "White"));
    }

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public DateTime BirthDate
    {
        set { SetProperty(ref birthDate, value); }
        get { return birthDate; }
    }

    public bool CodesInCSharp
    {
        set { SetProperty(ref codesInCSharp, value); }
        get { return codesInCSharp; }
    }

    public double NumberOfCopies
    {
        set { SetProperty(ref numberOfCopies, value); }
        get { return numberOfCopies; }
    }

    public NamedColor BackgroundNamedColor
    {
        set
        {
            if (SetProperty(ref backgroundNamedColor, value))
            {
                OnPropertyChanged("BackgroundColor");
            }
        }
        get { return backgroundNamedColor; }
    }

    public Color BackgroundColor
    {
        get { return BackgroundNamedColor?.Color ?? Color.White; }
    }

    public void SaveState(IDictionary<string, object> dictionary)
    {
        dictionary["Name"] = Name;
        dictionary["BirthDate"] = BirthDate;
        dictionary["CodesInCSharp"] = CodesInCSharp;
        dictionary["NumberOfCopies"] = NumberOfCopies;
        dictionary["BackgroundNamedColor"] = BackgroundNamedColor.Name;
    }

    T GetDictionaryEntry<T>(IDictionary<string, object> dictionary, string key, T defaultValue = default(T))
    {
        return dictionary.ContainsKey(key) ? (T)dictionary[key] : defaultValue;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Каждого параметра приложения — это свойство, которое сохраняется в словарь свойств Xamarin.Forms в метод с именем `SaveState` и загружен из этого словаря в конструкторе. В конце класса двумя способами, которые помогут упростить ViewModels и сделать их меньше подвержена ошибкам. `OnPropertyChanged` Метод внизу имеет необязательный параметр, который присваивается свойству вызывающего. Это позволяет избежать орфографические ошибки, при указании имени свойства в виде строки.

`SetProperty` Метод в классе, выполняет еще более: он сравнивает значение, которое задается для свойства со значением, хранящийся в виде поля, а только вызывает `OnPropertyChanged` при два значения не равны.

`SampleSettingsViewModel` Класс определяет два свойства для фонового цвета: `BackgroundNamedColor` свойство имеет тип `NamedColor`, который класс также входит в **DataBindingDemos** решения. `BackgroundColor` Свойство имеет тип `Color`и получается от `Color` свойство `NamedColor` объекта.

`NamedColor` Класс использует отражение .NET перечислить все открытые статические поля в Xamarin.Forms `Color` структуры и для их хранения с именами в коллекции, доступный из статического `All` свойство:

```csharp
public class NamedColor : IEquatable<NamedColor>, IComparable<NamedColor>
{
    // Instance members
    private NamedColor()
    {
    }

    public string Name { private set; get; }

    public string FriendlyName { private set; get; }

    public Color Color { private set; get; }

    public string RgbDisplay { private set; get; }

    public bool Equals(NamedColor other)
    {
        return Name.Equals(other.Name);
    }

    public int CompareTo(NamedColor other)
    {
        return Name.CompareTo(other.Name);
    }

    // Static members
    static NamedColor()
    {
        List<NamedColor> all = new List<NamedColor>();
        StringBuilder stringBuilder = new StringBuilder();

        // Loop through the public static fields of the Color structure.
        foreach (FieldInfo fieldInfo in typeof(Color).GetRuntimeFields())
        {
            if (fieldInfo.IsPublic &&
                fieldInfo.IsStatic &&
                fieldInfo.FieldType == typeof(Color))
            {
                // Convert the name to a friendly name.
                string name = fieldInfo.Name;
                stringBuilder.Clear();
                int index = 0;

                foreach (char ch in name)
                {
                    if (index != 0 && Char.IsUpper(ch))
                    {
                        stringBuilder.Append(' ');
                    }
                    stringBuilder.Append(ch);
                    index++;
                }

                // Instantiate a NamedColor object.
                Color color = (Color)fieldInfo.GetValue(null);

                NamedColor namedColor = new NamedColor
                {
                    Name = name,
                    FriendlyName = stringBuilder.ToString(),
                    Color = color,
                    RgbDisplay = String.Format("{0:X2}-{1:X2}-{2:X2}",
                                                (int)(255 * color.R),
                                                (int)(255 * color.G),
                                                (int)(255 * color.B))
                };

                // Add it to the collection.
                all.Add(namedColor);
            }
        }
        all.TrimExcess();
        all.Sort();
        All = all;
    }

    public static IList<NamedColor> All { private set; get; }

    public static NamedColor Find(string name)
    {
        return ((List<NamedColor>)All).Find(nc => nc.Name == name);
    }

    public static string GetNearestColorName(Color color)
    {
        double shortestDistance = 1000;
        NamedColor closestColor = null;

        foreach (NamedColor namedColor in NamedColor.All)
        {
            double distance = Math.Sqrt(Math.Pow(color.R - namedColor.Color.R, 2) +
                                        Math.Pow(color.G - namedColor.Color.G, 2) +
                                        Math.Pow(color.B - namedColor.Color.B, 2));

            if (distance < shortestDistance)
            {
                shortestDistance = distance;
                closestColor = namedColor;
            }
        }
        return closestColor.Name;
    }
}
```

`App` В класс **DataBindingDemos** проекта определяет свойство, именуемое `Settings` типа `SampleSettingsViewModel`. Это свойство инициализируется при `App` создается экземпляр класса и `SaveState` метод вызывается, когда `OnSleep` вызывается метод:

```csharp
public partial class App : Application
{
    public App()
    {
        InitializeComponent();

        Settings = new SampleSettingsViewModel(Current.Properties);

        MainPage = new NavigationPage(new MainPage());
    }

    public SampleSettingsViewModel Settings { private set; get; }

    protected override void OnStart()
    {
        // Handle when your app starts
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Settings.SaveState(Current.Properties);
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
    }
}
```

Дополнительные сведения о методах жизненного цикла приложения, см. в статье [ **жизненный цикл приложения**](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

Практически все остальное обрабатывается в **SampleSettingsPage.xaml** файла. `BindingContext` Страницы задается с помощью `Binding` расширение разметки: источник привязки является статическим `Application.Current` свойство, которое является экземпляром из `App` класс в проекте и `Path` присваивается `Settings` свойство, являющееся `SampleSettingsViewModel` объекта:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.SampleSettingsPage"
             Title="Sample Settings"
             BindingContext="{Binding Source={x:Static Application.Current},
                                      Path=Settings}">

    <StackLayout BackgroundColor="{Binding BackgroundColor}"
                 Padding="10"
                 Spacing="10">

        <StackLayout Orientation="Horizontal">
            <Label Text="Name: "
                   VerticalOptions="Center" />

            <Entry Text="{Binding Name}"
                   Placeholder="your name"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Birth Date: "
                   VerticalOptions="Center" />

            <DatePicker Date="{Binding BirthDate}"
                        HorizontalOptions="FillAndExpand"
                        VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Do you code in C#? "
                   VerticalOptions="Center" />

            <Switch IsToggled="{Binding CodesInCSharp}"
                    VerticalOptions="Center" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="Number of Copies: "
                   VerticalOptions="Center" />

            <Stepper Value="{Binding NumberOfCopies}"
                     VerticalOptions="Center" />

            <Label Text="{Binding NumberOfCopies}"
                   VerticalOptions="Center" />
        </StackLayout>

        <Label Text="Background Color:" />

        <ListView x:Name="colorListView"
                  ItemsSource="{x:Static local:NamedColor.All}"
                  SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
                  VerticalOptions="FillAndExpand"
                  RowHeight="40">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout Orientation="Horizontal">
                            <BoxView Color="{Binding Color}"
                                     HeightRequest="32"
                                     WidthRequest="32"
                                     VerticalOptions="Center" />

                            <Label Text="{Binding FriendlyName}"
                                   FontSize="24"
                                   VerticalOptions="Center" />
                        </StackLayout>                        
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Все дочерние элементы страницы наследуют контекст привязки. Большинство других привязок, на этой странице к свойствам в `SampleSettingsViewModel`. `BackgroundColor` Свойство используется для задания `BackgroundColor` свойство `StackLayout`и `Entry`, `DatePicker`, `Switch`, и `Stepper` свойства привязаны к другие свойства в ViewModel.

`ItemsSource` Свойство `ListView` присваивается статический `NamedColor.All` свойство. Это заполняет `ListView` со всеми `NamedColor` экземпляров. Для каждого элемента в `ListView`, контекст привязки для элемента имеет значение `NamedColor` объекта. `BoxView` И `Label` в `ViewCell` привязаны к свойствам в `NamedColor`.

`SelectedItem` Свойство `ListView` имеет тип `NamedColor`и привязывается к `BackgroundNamedColor` свойство `SampleSettingsViewModel`:

```xaml
SelectedItem="{Binding BackgroundNamedColor, Mode=TwoWay}"
```

Режим привязки по умолчанию для `SelectedItem` является `OneWayToSource`, который устанавливает свойство ViewModel из выбранного элемента. `TwoWay` Режим позволяет `SelectedItem` инициализированную из ViewModel.

Тем не менее, если `SelectedItem` устанавливается таким образом, `ListView` автоматически прокручивается для отображения выбранного элемента. Необходим немного кода в файл с выделенным кодом:

```csharp
public partial class SampleSettingsPage : ContentPage
{
    public SampleSettingsPage()
    {
        InitializeComponent();

        if (colorListView.SelectedItem != null)
        {
            colorListView.ScrollTo(colorListView.SelectedItem,
                                   ScrollToPosition.MakeVisible,
                                   false);
        }
    }
}
```

На снимке экрана iOS слева показана программа, при первом запуске. Конструктор в `SampleSettingsViewModel` инициализирует белый цвет фона, а это, что выбрано в `ListView`:

[![Примеры параметров](binding-mode-images/samplesettings-small.png "примеры параметров")](binding-mode-images/samplesettings-large.png#lightbox "примеры параметров")

На других двух снимках экрана показано измененных параметров. Экспериментируя с этой страницей, не забудьте поместить программу в спящий режим или завершить его на устройстве или эмуляторе, на котором он выполняется. Завершение программы из отладчика Visual Studio не приведет к `OnSleep` в Переопределите `App` класса.

В следующей статье будет показано, как указать [ **форматирования строки** ](string-formatting.md) привязок данных, заданных для `Text` свойство `Label`.


## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Данные привязки Глава из книги по Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
