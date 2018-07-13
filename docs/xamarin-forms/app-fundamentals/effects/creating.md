---
title: Создание эффекта
description: Эффекты упростить настройку элемента управления. В этой статье показано, как для создания эффекта, который изменяет цвет фона элемента управления ввода, когда элемент управления получает фокус.
ms.prod: xamarin
ms.assetid: 9E2C8DB0-36A2-4F13-8E3C-A66D7021DB13
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2016
ms.openlocfilehash: b29d83999724a35293882f7b9efc0158171c4fd2
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998164"
---
# <a name="creating-an-effect"></a>Создание эффекта

_Эффекты упростить настройку элемента управления. В этой статье показано, как для создания эффекта, который изменяет цвет фона элемента управления ввода, когда элемент управления получает фокус._

Процесс создания эффекта в каждом проекте под конкретную платформу выглядит следующим образом:

1. Создать подкласс `PlatformEffect` класса.
1. Переопределить `OnAttached` логику метода и запись для настройки элемента управления.
1. Переопределить `OnDetached` логику метода и записи для очистки Настройка элементов управления, если это необходимо.
1. Добавить [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) эффект классу атрибут. Этот атрибут задает пространство имен компании широкий для эффектов, предотвращение конфликтов с другими эффекты с тем же именем. Обратите внимание на то, что этот атрибут может применяться только один раз на каждый проект.
1. Добавить [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) эффект классу атрибут. Этот атрибут регистрирует эффект уникальный идентификатор, который используется с Xamarin.Forms, а также имя группы для поиска эффект перед ее применением к элементу управления. Атрибут принимает два параметра — имя типа, эффект и уникальная строка, которая будет использоваться для поиска эффект перед ее применением к элементу управления.

Эффект можно затем использовать, подключив его к соответствующий элемент управления.

> [!NOTE]
> Это необязательно для предоставления эффекта в каждом проекте платформы. Попытка использовать эффект, когда один не зарегистрировано вернет значение отличное от null, не выполняет никаких действий.

Пример приложения демонстрирует `FocusEffect` , изменяет цвет фона элемента управления, когда он получает фокус. На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](creating-images/focus-effect.png "Обязанности проекта эффект фокусировки")

[ `Entry` ](xref:Xamarin.Forms.Entry) Управления `HomePage` настраивается путем `FocusEffect` класс в каждом проекте под конкретную платформу. Каждый `FocusEffect` класс является производным от `PlatformEffect` класса для каждой платформы. В результате `Entry` управления, отображаемого с цветом фона платформы, который изменяет элемент управления получает фокус, как показано на следующем снимке экрана:

![](creating-images/screenshots-1.png "Сосредоточиться эффект на каждой платформе")
![](creating-images/screenshots-2.png "сосредоточиться эффект на каждой платформе")

## <a name="creating-the-effect-on-each-platform"></a>Создание эффекта на каждой платформе

В следующих разделах рассматривается реализация платформы `FocusEffect` класса.

## <a name="ios-project"></a>Проект iOS

В следующем коде показано в примере `FocusEffect` реализации для проекта iOS:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.iOS
{
    public class FocusEffect : PlatformEffect
    {
        UIColor backgroundColor;

        protected override void OnAttached ()
        {
            try {
                Control.BackgroundColor = backgroundColor = UIColor.FromRGB (204, 153, 255);
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);

            try {
                if (args.PropertyName == "IsFocused") {
                    if (Control.BackgroundColor == backgroundColor) {
                        Control.BackgroundColor = UIColor.White;
                    } else {
                        Control.BackgroundColor = backgroundColor;
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached` Метода задает `BackgroundColor` свойство элемента управления, сиреневый с `UIColor.FromRGB` метода, а также сохраняет этот цвет в поле. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, эффект присоединяется к не `BackgroundColor` свойство. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

`OnElementPropertyChanged` Переопределение реагирует на изменения свойство, используемое в элементе управления Xamarin.Forms. Когда [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) изменения свойств `BackgroundColor` свойство элемента управления изменяется на белый, если элемент управления имеет фокус, в противном случае она принимает значение сиреневый. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, эффект присоединяется к не `BackgroundColor` свойство.

## <a name="android-project"></a>Проект Android

В следующем коде показано в примере `FocusEffect` реализацию для проекта Android:

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly:ResolutionGroupName ("MyCompany")]
[assembly:ExportEffect (typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.Droid
{
    public class FocusEffect : PlatformEffect
    {
        Android.Graphics.Color backgroundColor;

        protected override void OnAttached ()
        {
            try {
                backgroundColor = Android.Graphics.Color.LightGreen;
                Control.SetBackgroundColor (backgroundColor);

            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached ()
        {
        }

        protected override void OnElementPropertyChanged (System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged (args);
            try {
                if (args.PropertyName == "IsFocused") {
                    if (((Android.Graphics.Drawables.ColorDrawable)Control.Background).Color == backgroundColor) {
                        Control.SetBackgroundColor (Android.Graphics.Color.Black);
                    } else {
                        Control.SetBackgroundColor (backgroundColor);
                    }
                }
            } catch (Exception ex) {
                Console.WriteLine ("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

`OnAttached` Вызовы методов `SetBackgroundColor` метод, чтобы задать цвет фона элемента управления, светло-зеленый, а также сохраняет этот цвет в поле. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, эффект присоединяется к не `SetBackgroundColor` свойство. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

`OnElementPropertyChanged` Переопределение реагирует на изменения свойство, используемое в элементе управления Xamarin.Forms. Когда [ `IsFocused` ](xref:Xamarin.Forms.VisualElement.IsFocused) изменений свойств, цвет фона элемента управления изменяется на белый, если элемент управления имеет фокус, в противном случае оно изменится на светло-зеленый. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, эффект присоединяется к не `BackgroundColor` свойство.

## <a name="universal-windows-platform-projects"></a>Проекты универсальной Windows платформы

В следующем коде показано в примере `FocusEffect` реализации для проектов универсальной платформы Windows (UWP):

```csharp
using Xamarin.Forms;
using Xamarin.Forms.Platform.UWP;

[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(FocusEffect), "FocusEffect")]
namespace EffectsDemo.UWP
{
    public class FocusEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            try
            {
                (Control as Windows.UI.Xaml.Controls.Control).Background = new SolidColorBrush(Colors.Cyan);
                (Control as FormsTextBox).BackgroundFocusBrush = new SolidColorBrush(Colors.White);
            }
            catch (Exception ex)
            {
                Debug.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }

        protected override void OnDetached()
        {
        }
    }
}
```

`OnAttached` Метода задает `Background` свойство элемента управления голубой и задающего `BackgroundFocusBrush` свойство на белый. Эта функция заключается в `try` / `catch` блокировки в случае, если элемент управления, эффект присоединяется к не предоставляет эти свойства. Отсутствует реализация обеспечивается `OnDetached` метод так, как очистка не требуется.

## <a name="consuming-the-effect"></a>Использование эффекта

Процесс получения эффекта из Xamarin.Forms .NET Standard библиотеки или общую библиотеку проекта выглядит следующим образом:

1. Объявите элемент управления, который будет настраиваться определяется результатами.
1. Присоединение эффект к элементу управления, добавив его к элементу управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции.

> [!NOTE]
> Экземпляр эффект можно подключить только для одного элемента управления. Таким образом необходимо устранить эффект дважды, чтобы использовать его на два элемента управления.

## <a name="consuming-the-effect-in-xaml"></a>Использование эффекта в XAML

В следующем примере показан код XAML [ `Entry` ](xref:Xamarin.Forms.Entry) элемента управления, к которому `FocusEffect` подключен:

```xaml
<Entry Text="Effect attached to an Entry" ...>
    <Entry.Effects>
        <local:FocusEffect />
    </Entry.Effects>
    ...
</Entry>
```

`FocusEffect` Класса в библиотеке .NET Standard поддерживает эффект потребления в XAML и показано в следующем примере кода:

```csharp
public class FocusEffect : RoutingEffect
{
    public FocusEffect () : base ("MyCompany.FocusEffect")
    {
    }
}
```

`FocusEffect` Подклассы класса [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) класс, который представляет эффект независимо от платформы, который создает оболочку для внутреннего эффект, обычно от платформы. `FocusEffect` Класс вызывает конструктор базового класса, передав в качестве параметра, состоящий из объединения имени группы разрешения (с использованием [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) атрибут класса эффект), и уникальный идентификатор, создаваемый был указан с помощью [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) атрибут класса эффект. Таким образом, когда [ `Entry` ](xref:Xamarin.Forms.Entry) инициализируется во время выполнения, новый экземпляр класса `MyCompany.FocusEffect` добавляется к элементу управления [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции.

Эффекты можно также прикреплять к элементам управления с помощью поведения или с помощью присоединенных свойств. Дополнительные сведения о присоединении эффекта к элементу управления с помощью поведения, см. в разделе [EffectBehavior для повторного использования](~/xamarin-forms/app-fundamentals/behaviors/reusable/effect-behavior.md). Дополнительные сведения о присоединении эффекта к элементу управления с помощью присоединенных свойств, см. в разделе [передача параметров эффект](~/xamarin-forms/app-fundamentals/effects/passing-parameters/index.md).

## <a name="consuming-the-effect-in-cnum"></a>Использование эффекта в C&num;

Эквивалент [ `Entry` ](xref:Xamarin.Forms.Entry) в C# показан в следующем примере кода:

```csharp
var entry = new Entry {
  Text = "Effect attached to an Entry",
  ...
};
```

`FocusEffect` Присоединяется к `Entry` экземпляра путем добавления элемента управления эффект [ `Effects` ](xref:Xamarin.Forms.Element.Effects) коллекции, как показано в следующем примере кода:

```csharp
public HomePageCS ()
{
  ...
  entry.Effects.Add (Effect.Resolve ("MyCompany.FocusEffect"));
  ...
}
```

[ `Effect.Resolve` ](xref:Xamarin.Forms.Effect.Resolve(System.String)) Возвращает [ `Effect` ](xref:Xamarin.Forms.Effect) для указанного имени, который представляет собой объединение имени группы разрешения (с использованием [ `ResolutionGroupName` ](xref:Xamarin.Forms.ResolutionGroupNameAttribute) атрибут класса эффект) и уникальный идентификатор, который был указан с помощью [ `ExportEffect` ](xref:Xamarin.Forms.ExportEffectAttribute) атрибут класса эффект. Если платформа не предоставляет эффектом `Effect.Resolve` метод будет возвращать значение отличное от`null` значение.

## <a name="summary"></a>Сводка

В этой статье было показано, как для создания эффекта, которое изменяет цвет фона [ `Entry` ](xref:Xamarin.Forms.Entry) управления, когда элемент управления получает фокус.


## <a name="related-links"></a>Связанные ссылки

- [Пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
- [Эффект](xref:Xamarin.Forms.Effect)
- [PlatformEffect](xref:Xamarin.Forms.PlatformEffect`2)
- [Эффект цвета фона (пример)](https://developer.xamarin.com/samples/xamarin-forms/effects/backgroundcoloreffect/)
- [Эффект фокусировки (пример)](https://developer.xamarin.com/samples/xamarin-forms/effects/focuseffect/)
