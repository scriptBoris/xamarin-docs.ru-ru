---
title: В случае ошибки привязки Xamarin.Forms
description: В этой статье объясняется, как для повышения надежности привязок, определив возврата значений, которые будут использоваться, если привязка не выполняется.
ms.prod: xamarin
ms.assetid: 637ACD9D-3E5D-4014-86DE-A77D1FEF238A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: fa375720730630065609e328b343e16578c6f1df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50131995"
---
# <a name="xamarinforms-binding-fallbacks"></a>В случае ошибки привязки Xamarin.Forms

Иногда сбой привязки данных, так как источник привязки не может быть разрешена, или привязка выполняется успешно, но возвращает `null` значение. Хотя эти сценарии могут обрабатываться с помощью преобразователей значений или других дополнительный код, привязки данных можно сделать более надежным, определив возврата значений, используемых при сбое процесса привязки. Это можно сделать, определив [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) и [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) свойствам в выражении привязки. Так как эти свойства находятся в [ `BindingBase` ](xref:Xamarin.Forms.BindingBase) класса, они могут использоваться с привязками, скомпилированный привязки и с `Binding` расширение разметки.

> [!NOTE]
> Использование [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) и [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) свойствам в выражении привязки является необязательным.

## <a name="defining-a-fallback-value"></a>Определение резервного значения

[ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) Свойство позволяет запасное значение должна определяться, который будет использоваться при привязке *источника* не может быть разрешен. Распространенный сценарий для этого свойства является привязка к свойствам источников, которые могут не существовать для всех объектов в присоединенной коллекции разнородных типов.

**MonkeyDetail** страницы показан параметр [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) свойство:

```xaml
<Label Text="{Binding Population, FallbackValue='Population size unknown'}"
       ... />   
```

Привязка на [ `Label` ](xref:Xamarin.Forms.Label) определяет [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) значение, которое будет задано для целевого объекта, если источник привязки не может быть разрешена. Таким образом, значения, определенного `FallbackValue` свойство будет отображаться, если `Population` свойство не существует в привязанном объекте. Обратите внимание, что здесь `FallbackValue` значение свойства разделяются одинарными кавычками (апостроф).

Вместо того чтобы [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) значения встроенного свойства рекомендуется определять их как ресурсы в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Преимуществом такого подхода является такие значения будут определены один раз в одном месте что могут более легко быть локализованы. Ресурсы может быть извлечен с помощью `StaticResource` расширение разметки:

```xaml
<Label Text="{Binding Population, FallbackValue={StaticResource populationUnknown}}"
       ... />  
```

> [!NOTE]
> Невозможно присвоить `FallbackValue` свойство с выражением привязки.

Вот ее запуск на всех трех платформах:

![Привязка FallbackValue](binding-fallbacks-images/bindingunavailable-detail-cropped.png "FallbackValue привязки")

Когда `FallbackValue` свойства не задано в выражении привязки и путь привязки или часть пути не разрешается, [ `BindableProperty.DefaultValue` ](xref:Xamarin.Forms.BindableProperty.DefaultValue) установлено на целевом объекте. Тем не менее, если `FallbackValue` свойства, а путь привязки или часть пути не разрешается, значение `FallbackValue` свойство value устанавливается на целевом объекте. Таким образом, на **MonkeyDetail** страницы [ `Label` ](xref:Xamarin.Forms.Label) отображает «Неизвестный размер совокупности», так как не имеет связанного объекта `Population` свойство.

> [!IMPORTANT]
> Преобразователь значений, определенных в выражении привязки не выполняется при [ `FallbackValue` ](xref:Xamarin.Forms.BindingBase.FallbackValue) свойству.

## <a name="defining-a-null-replacement-value"></a>Определение значения null замены

[ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) Свойство позволяет значение замены должна определяться, который будет использоваться при привязки *источника* будет устранена, но будет `null`. Распространенный сценарий для этого свойства является привязка к свойствам источников, которые могут быть `null` в связанной коллекции.

**Кодеры** страницы показан параметр [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) свойство:

```xaml
<ListView ItemsSource="{Binding Monkeys}"
          ...>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Image Source="{Binding ImageUrl, TargetNullValue='https://upload.wikimedia.org/wikipedia/commons/2/20/Point_d_interrogation.jpg'}"
                           ... />
                    ...
                    <Label Text="{Binding Location, TargetNullValue='Location unknown'}"
                           ... />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Привязки на [ `Image` ](xref:Xamarin.Forms.Image) и [ `Label` ](xref:Xamarin.Forms.Label) оба определения [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) значения, которые будут применяться, если путь привязки возвращает `null`. Таким образом, значения, которые были определены `TargetNullValue` для любых объектов в коллекции будут выведены свойства где `ImageUrl` и `Location` свойства не определены. Обратите внимание, что здесь `TargetNullValue` значения свойств, разделенные символами одинарной кавычки (апостроф).

Вместо того чтобы [ `TargetNullValue` ](xref:Xamarin.Forms.BindingBase.TargetNullValue) значения встроенного свойства рекомендуется определять их как ресурсы в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Преимуществом такого подхода является такие значения будут определены один раз в одном месте что могут более легко быть локализованы. Ресурсы может быть извлечен с помощью `StaticResource` расширение разметки:

```xaml
<Image Source="{Binding ImageUrl, TargetNullValue={StaticResource fallbackImageUrl}}"
       ... />
<Label Text="{Binding Location, TargetNullValue={StaticResource locationUnknown}}"
       ... />
```

> [!NOTE]
> Невозможно присвоить `TargetNullValue` свойство с выражением привязки.

Вот ее запуск на всех трех платформах:

[![Привязка TargetNullValue](binding-fallbacks-images/bindingunavailable-small.png "привязки TargetNullValue")](binding-fallbacks-images/bindingunavailable-large.png#lightbox "TargetNullValue привязки")

При `TargetNullValue` свойства не задано в выражении привязки, значение источника `null` будут преобразованы в том случае, если определен преобразователь значений, отформатированных при `StringFormat` определен, и результат затем устанавливается на целевом объекте. Тем не менее, если `TargetNullValue` свойство задано, значение источника `null` будут преобразованы в том случае, если определен преобразователь значений, и в том случае, если по-прежнему `null` после преобразования значение `TargetNullValue` установлено на целевом объекте.

> [!IMPORTANT]
> Форматирование строк в выражении привязки не применяется при `TargetNullValue` свойству.

## <a name="related-links"></a>Связанные ссылки

- [Демонстрации, привязка данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
