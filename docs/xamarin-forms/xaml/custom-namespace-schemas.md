---
title: Схемы пользовательского пространства имен XAML в Xamarin.Forms
description: Схема пользовательского пространства имен XAML может быть определена с помощью XmlnsDefinitionAttribute класса, который задает сопоставление между пользовательский URL-адрес и один или несколько пространств имен CLR. Затем схемы пользовательского пространства имен можно использовать в объявлениях пространств имен XAML.
ms.prod: xamarin
ms.assetid: FDF201A1-8C35-4569-A728-F9B0A0C5B31A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/21/2018
ms.openlocfilehash: 8167ff00d3e4d7167772f6f5a578da6197c0d72d
ms.sourcegitcommit: 93c9fe61eb2cdfa530960b4253eb85161894c882
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55832232"
---
# <a name="xaml-custom-namespace-schemas-in-xamarinforms"></a>Схемы пользовательского пространства имен XAML в Xamarin.Forms

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)

В библиотеке можно ссылаться на типы в XAML объявление пространства имен XAML для библиотеки, объявление пространства имен, указав имя пространства имен Common Language Runtime (CLR) и имя сборки:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:MyCompany.Controls;assembly="MyCompany.Controls">
    ...
</ContentPage>
```

Тем не менее, указав имя пространства имен и сборки среды CLR в `xmlns` определение может быть неудобно и подвержено ошибкам. Кроме того несколько объявлений пространства имен XAML может потребоваться Если библиотека содержит типы в нескольких пространствах имен.

Альтернативным подходом является определение пользовательского пространства имен схемы, таких как `http://mycompany.com/schemas/controls`, который сопоставляется один или несколько пространств имен CLR. Это позволяет одно объявление пространства имен XAML для ссылки на все типы в сборке, даже если они находятся в разных пространствах имен. Он также позволяет одно объявление пространства имен XAML для ссылочных типов в нескольких сборках.

Дополнительные сведения о пространствах имен XAML см. в разделе [пространства имен XAML в Xamarin.Forms](namespaces.md).

## <a name="defining-a-custom-namespace-schema"></a>Определение пользовательского пространства имен схемы

Пример приложения содержит библиотеку, которая предоставляет некоторые простые элементы управления, такие как `CircleButton`:

```csharp
using Xamarin.Forms;

namespace MyCompany.Controls
{
    public class CircleButton : Button
    {
        ...
    }
}
```

Все элементы управления в библиотеке находятся в `MyCompany.Controls` пространства имен. Эти элементы управления могут предоставляться для вызывающей сборки через пользовательского пространства имен схемы.

Схема пользовательского пространства имен определяется с помощью `XmlnsDefinitionAttribute` класс, который задает сопоставление между пространством имен XAML и один или несколько пространств имен CLR. `XmlnsDefinitionAttribute` Принимает два аргумента: имя пространства имен XAML и имя пространства имен CLR. Имя пространства имен XAML хранится в `XmlnsDefinitionAttribute.XmlNamespace` свойство и имя пространства имен CLR хранится в `XmlnsDefinitionAttribute.ClrNamespace` свойство.

> [!NOTE]
> `XmlnsDefinitionAttribute` Класс также имеет свойство с именем `AssemblyName`, который можно дополнительно задать имя сборки. Это только требуется, если пространство имен среды CLR, на которые ссылается `XmlnsDefinitionAttribute` в внешней сборки.

`XmlnsDefinitionAttribute` Должен быть определен на уровне сборки в проект, содержащий пространства имен CLR, который будет сопоставлен в схеме пользовательского пространства имен. В следующем примере показан **AssemblyInfo.cs** файл из примера приложения:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

[assembly: Preserve]
[assembly: XmlnsDefinition("http://mycompany.com/schemas/controls", "MyCompany.Controls")]
```

Этот код создает схему пользовательского пространства имен, которая сопоставляет `http://mycompany.com/schemas/controls` URL-адрес для `MyCompany.Controls` пространство имен CLR. Кроме того `Preserve` атрибут указан на сборку, чтобы убедиться, что компоновщик сохраняет все типы в сборке.

> [!IMPORTANT]
> `Preserve` Атрибут должен применяться к классам в сборке, сопоставляются с помощью пользовательского пространства имен схемы, или применяется ко всей сборке.

Затем схемы пользовательского пространства имен можно использовать для разрешения типов в файлах XAML.

## <a name="consuming-a-custom-namespace-schema"></a>Использование пользовательского пространства имен схемы

Для использования типов из схемы пользовательского пространства имен, компилятор XAML необходимо, присутствует ссылка код из сборки, которая использует типы на сборку, которая определяет типы. Это можно сделать путем добавления класса, содержащего `Init` метод в сборке, определяющей типы, которые будут использоваться через XAML:

```csharp
namespace MyCompany.Controls
{
    public static class Controls
    {
        public static void Init()
        {
        }
    }
}
```

`Init` Затем метод может вызываться из сборки, которая использует типы из пользовательского пространства имен схемы:

```csharp
using Xamarin.Forms;
using MyCompany.Controls;

namespace CustomNamespaceSchemaDemo
{
    public partial class MainPage : ContentPage
    {
        public MainPage()
        {
            Controls.Init();
            InitializeComponent();
        }
    }
}
```

> [!WARNING]
> Невозможность включить такую ссылку кода приведет к компилятор XAML, когда не удается найти сборку, содержащую типы схемы пользовательского пространства имен.

Для использования `CircleButton` объявляется элемент управления, пространство имен XAML, с помощью объявления пространства имен, указав URL-адрес пользовательского пространства имен схемы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="http://mycompany.com/schemas/controls"
             x:Class="CustomNamespaceSchemaDemo.MainPage">
    <StackLayout Margin="20,35,20,20">
        ...
        <controls:CircleButton Text="+"
                               BackgroundColor="Fuchsia"
                               BorderColor="Black"
                               CircleDiameter="100" />
        <controls:CircleButton Text="-"
                               BackgroundColor="Teal"
                               BorderColor="Silver"
                               CircleDiameter="70" />
        ...
    </StackLayout>
</ContentPage>
```

`CircleButton` Затем можно добавить экземпляры для [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) , объявляя их с `controls` префикс пространства имен.

Чтобы найти пространство имен пользовательских типов схемы, Xamarin.Forms выполняет поиск указанных сборок для `XmlnsDefinitionAttribute` экземпляров. Если `xmlns` атрибут для элемента в файле XAML соответствует `XmlNamespace` значение свойства в `XmlnsDefinitionAttribute`, Xamarin.Forms будет пытаться использовать `XmlnsDefinitionAttribute.ClrNamespace` значение свойства для разрешения типа. Если тип не удается, Xamarin.Forms продолжит попытки разрешения типов, на основе дополнительные сопоставления `XmlnsDefinitionAttribute` экземпляров.

Результат — что два `CircleButton` отображаются экземпляры:

![Круг на кнопки](custom-namespace-schemas-images/circle-buttons.png "круг кнопок")

## <a name="related-links"></a>Связанные ссылки

- [Пользовательские схемы пространства имен (пример)](https://developer.xamarin.com/samples/xamarin-forms/XAML/CustomNamespaceSchemas/)
- [Пространства имен XAML в Xamarin.Forms](namespaces.md)
