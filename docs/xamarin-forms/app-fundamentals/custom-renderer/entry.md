---
title: Настройка записи
description: Элемент управления Xamarin.Forms запись позволяет одна строка текста для редактирования. В этой статье показано, как создать пользовательское средство отрисовки для записи элемента управления, что позволяет разработчикам переопределять способ отрисовки собственного по умолчанию с параметрами, свои собственные платформы.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 30326b8d52f39268015bdcbee1b84b9d9e5516b9
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998964"
---
# <a name="customizing-an-entry"></a>Настройка записи

_Элемент управления Xamarin.Forms запись позволяет одна строка текста для редактирования. В этой статье показано, как создать пользовательское средство отрисовки для записи элемента управления, что позволяет разработчикам переопределять способ отрисовки собственного по умолчанию с параметрами, свои собственные платформы._

Каждый элемент управления Xamarin.Forms имеет сопутствующий модуль подготовки отчетов для каждой платформы, который создает экземпляр собственного элемента управления. Когда [ `Entry` ](xref:Xamarin.Forms.Entry) элемент управления отрисовывается в приложении Xamarin.Forms в iOS `EntryRenderer` создается экземпляр класса, который, в создает собственный `UITextField` элемента управления. На платформе Android `EntryRenderer` создает экземпляр класса `EditText` элемента управления. В универсальной платформы Windows (UWP), `EntryRenderer` создает экземпляр класса `TextBox` элемента управления. Дополнительные сведения о визуализации и классы собственного элемента управления, которые сопоставляются с элементами управления Xamarin.Forms, см. в разделе [модуль подготовки отчетов базовые классы и собственные элементы управления](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

На следующей схеме показана связь между [ `Entry` ](xref:Xamarin.Forms.Entry) управления и соответствующие собственные элементы управления, которые реализуют его:

![](entry-images/entry-classes.png "Связь между управление ввода и реализация собственных элементов управления")

Процесс подготовки отчета можно задействуем преимущества реализации настроек конкретных платформ путем создания пользовательского средства визуализации для [ `Entry` ](xref:Xamarin.Forms.Entry) управления на каждой платформе. Таким образом процесс выглядит следующим образом:

1. [Создание](#Creating_the_Custom_Entry_Control) пользовательского элемента управления Xamarin.Forms.
1. [Использовать](#Consuming_the_Custom_Control) пользовательского элемента управления с Xamarin.Forms.
1. [Создание](#Creating_the_Custom_Renderer_on_each_Platform) пользовательское средство отрисовки для элемента управления на каждой платформе.

Каждый элемент теперь обсуждаются в свою очередь, для реализации [ `Entry` ](xref:Xamarin.Forms.Entry) элемента управления, имеющего другой цвет фона на каждой платформе.

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Создание элемента управления пользовательского ввода

Пользовательский [ `Entry` ](xref:Xamarin.Forms.Entry) элемент управления может создаваться путем создания подклассов `Entry` контролировать, как показано в следующем примере кода:

```csharp
public class MyEntry : Entry
{
}
```

`MyEntry` Управления создается в проекте библиотеки .NET Standard и просто [ `Entry` ](xref:Xamarin.Forms.Entry) элемента управления. Настройка элемента управления будет осуществляться в пользовательское средство отрисовки, поэтому нет дополнительных реализации не требуется в `MyEntry` элемента управления.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Использование пользовательского элемента управления

`MyEntry` Управления можно ссылаться в XAML в проекте библиотеки .NET Standard, объявление пространства имен для его расположения и используя префикс пространства имен в элементе управления. В следующем примере кода показано как `MyEntry` управления могут использоваться страницы XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

`local` Префикс пространства имен может быть присвоено любое имя. Тем не менее `clr-namespace` и `assembly` значения должны соответствовать данные пользовательского элемента управления. После объявления пространства имен префикс используется для ссылки на пользовательский элемент управления.

В следующем примере кода показано как `MyEntry` управления могут использоваться страницей C#:

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

Этот код создает новый экземпляр [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) объект, который будет отображать [ `Label` ](xref:Xamarin.Forms.Label) и `MyEntry` элемента управления по центру и по вертикали и горизонтали на странице.

Пользовательское средство отрисовки теперь могут добавляться в каждый проект приложения для настройки внешнего вида элемента управления на каждой платформе.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Создание пользовательского средства визуализации на каждой платформе

Процесс создания класса пользовательского средства визуализации выглядит следующим образом:

1. Создать подкласс `EntryRenderer` класс, который выполняет визуализацию собственного элемента управления.
1. Переопределить `OnElementChanged` метод, который использует собственный элемент управления и записи логику для настройки элемента управления. Этот метод вызывается, когда создается соответствующий элемент управления Xamarin.Forms.
1. Добавление `ExportRenderer` атрибут в класс пользовательского средства визуализации, чтобы указать, что он будет использоваться для отрисовки элемента управления Xamarin.Forms. Этот атрибут используется для регистрации пользовательского средства визуализации с помощью Xamarin.Forms.

> [!NOTE]
> Это необязательно для предоставления пользовательского средства визуализации в каждом проекте платформы. Если не зарегистрировано пользовательское средство отрисовки, то будет использоваться модуль подготовки отчетов по умолчанию для базового класса элемента управления.

На следующей схеме показана обязанности каждого проекта в примере приложения, а также связи между ними:

![](entry-images/solution-structure.png "Обязанности проекта MyEntry пользовательский модуль подготовки отчетов")

`MyEntry` Элемент управления отрисовывается, специфические для платформы `MyEntryRenderer` классы, которые являются производными от `EntryRenderer` класс для каждой платформы. Это приводит к каждой `MyEntry` управления выводом цвет фона для конкретных платформ, как показано на следующем снимке экрана:

![](entry-images/screenshots.png "Элемент управления MyEntry на каждой платформе")

`EntryRenderer` Предоставляет `OnElementChanged` метод, который вызывается, когда создается элемент управления Xamarin.Forms для отрисовки соответствующего собственного элемента управления. Этот метод принимает `ElementChangedEventArgs` параметр, содержащий `OldElement` и `NewElement` свойства. Эти свойства представляют собой элемент Xamarin.Forms, модуль подготовки отчетов *был* присоединен и элемент Xamarin.Forms, модуль подготовки отчетов *является* подключен, соответственно. В примере приложения `OldElement` свойство будет иметь `null` и `NewElement` свойство будет содержать ссылку на `MyEntry` элемента управления.

Переопределенная версия `OnElementChanged` метод в `MyEntryRenderer` класс — это место для этого собственный элемент управления. Типизированную ссылку на собственный элемент управления, используется на платформе может осуществляться через `Control` свойство. Кроме того, можно получить ссылку на элемент управления Xamarin.Forms, который готовится к просмотру с помощью `Element` свойство, несмотря на то, что он не используется в примере приложения.

Каждый класс пользовательского средства визуализации снабжен `ExportRenderer` атрибут, который регистрирует модуль подготовки отчетов с помощью Xamarin.Forms. Атрибут принимает два параметра — имя типа для отображаемого элемента управления Xamarin.Forms и имя типа пользовательского модуля подготовки отчетов. `assembly` Префикс в атрибут указывает, что атрибут применяется ко всей сборке.

В следующих разделах рассматривается реализация каждой платформы `MyEntryRenderer` класс пользовательского средства визуализации.

### <a name="creating-the-custom-renderer-on-ios"></a>Создание пользовательского средства визуализации на iOS

В следующем примере кода показано пользовательское средство отрисовки для платформы iOS:

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

Вызов базового класса `OnElementChanged` метод создает экземпляр iOS `UITextField` управления со ссылкой на элемент управления, назначаемый модуль подготовки отчетов `Control` свойство. Цвет фона, затем устанавливается сиреневый с `UIColor.FromRGB` метод.

### <a name="creating-the-custom-renderer-on-android"></a>Создание пользовательского средства визуализации на устройстве Android

В следующем примере кода показано пользовательское средство отрисовки для платформы Android:

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

Вызов базового класса `OnElementChanged` метод создает экземпляр Android `EditText` управления со ссылкой на элемент управления, назначаемый модуль подготовки отчетов `Control` свойство. Цвет фона устанавливается в светло-зеленый с `Control.SetBackgroundColor` метод.

### <a name="creating-the-custom-renderer-on-uwp"></a>Создание пользовательского средства визуализации на UWP

В следующем примере кода показано пользовательское средство отрисовки для универсальной платформы Windows:

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

Вызов базового класса `OnElementChanged` метод создает экземпляр `TextBox` управления со ссылкой на элемент управления, назначаемый модуль подготовки отчетов `Control` свойство. Цвет фона задайте голубой, создав `SolidColorBrush` экземпляра.

## <a name="summary"></a>Сводка

В этой статье показано, как создать пользовательский элемент управления средства визуализации для Xamarin.Forms [ `Entry` ](xref:Xamarin.Forms.Entry) элемента управления, что позволяет разработчикам переопределять способ отрисовки по умолчанию собственного с отрисовку, специфические для платформы. Пользовательские модули подготовки отчетов предоставляют эффективный подход к настройке внешнего вида элементов управления Xamarin.Forms. Они могут использоваться для изменения небольшой стилей или сложный макет платформы и настройка поведения.


## <a name="related-links"></a>Связанные ссылки

- [CustomRendererEntry (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
