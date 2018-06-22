---
title: Производительность элемента управления ListView
description: Хотя ListView — это полезное представление для отображения данных, она имеет некоторые ограничения. В этой статье объясняется, как обеспечить высокую производительность с помощью Xamarin.Forms ListView, в приложении.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: f1707a6b2a1dc03ae1346520bf29ff83f0fe74fb
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309815"
---
# <a name="listview-performance"></a>Производительность элемента управления ListView

При написании приложений для мобильных устройств, производительность имеет значение. Пользователи привыкли плавная прокрутка и время быстрой загрузки. Несоответствие требованиям ожиданий пользователей будет стоить оценки в хранилище приложения или в случае с бизнес-приложением затрат время и деньги.

Несмотря на то что [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) — это мощная представление для отображения данных, она имеет некоторые ограничения. Прокрутка производительность может снизиться при использовании пользовательских ячеек, особенно в том случае, если они содержат большим уровнем вложенности представление иерархии или использовать определенные макеты, требующие много измерения. К счастью существуют методы, которые можно использовать, чтобы избежать низкой производительности.

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Стратегия кэширования

Представлениях ListView часто используются для отображения данных гораздо больше, чем можно вписать на экране. Например, рассмотрим приложении музыки. Возможно, библиотека песен тысячи записей. Простой подход, который следует создать строку для каждой песни, будет иметь низкую производительность. Этот подход много ценных памяти и может привести к снижению прокрутки для сканирования. Другой подход заключается в том, чтобы создавать и уничтожать строк как данных в области просмотра. Требуется постоянное создание экземпляра и очистки объектов представления, которые могут быть очень медленно.

Для экономии памяти собственный [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) эквиваленты для каждой платформы имеют встроенные функции для повторного использования строк. Только ячейки, видимым на экране, загружаются в память и **содержимого** загружается в существующие ячейки. Это предотвращает приложение от необходимости создания экземпляра тысяч объектов, экономя время и память.

Xamarin.Forms позволяет [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) ячейки повторного использования через [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) перечисления, который имеет следующие значения:

```csharp
public enum ListViewCachingStrategy
{
  RetainElement,   // the default value
  RecycleElement,
  RecycleElementAndDataTemplate
}
```

> [!NOTE]
> Игнорирует универсальной платформы Windows (UWP) [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) кэширование стратегии, поскольку он всегда использует кэширования для повышения производительности. Таким образом, по умолчанию он ведет себя как если бы [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) применяется стратегия кэширования.

### <a name="retainelement"></a>RetainElement

[ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) Стратегию кэширования указывает, что [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) создаст ячейки для каждого элемента в списке, и значение по умолчанию `ListView` поведение. Обычно он должен использоваться в следующих случаях:

- Когда каждая ячейка имеет большое количество привязок (20-30 +).
- При изменении часто шаблона ячеек.
- Если проверка показывает, что `RecycleElement` кэширования результатов стратегии в снижение быстродействия.

Очень важно для распознавания последствия [ `RetainElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RetainElement/) стратегию кэширования при работе с пользовательские ячейки. Любой код инициализации ячейки необходимо выполнить для создания каждой ячейки, которое может быть несколько раз в секунду. В этом случае методы макета, которые были правильно на странице, как и с помощью нескольких вложенных [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) экземпляров становятся узкие места по производительности, когда они настраиваются и уничтожается в режиме реального времени прокрутке пользователем.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

[ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) Стратегию кэширования указывает, что [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) попытается уменьшить скорость место, занимаемое и выполнения его памяти путем перезапуска ячейки списка. Этот режим не всегда обеспечивает более высокую производительность и определить, какие улучшения необходимо проводить тестирование. Тем не менее оно обычно является предпочтительным вариантом выбора и должен использоваться в следующих случаях:

- Когда каждая ячейка имеет небольших и среднее количество привязок.
- При каждой ячейки [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) определяет все ячейки данных.
- При каждой ячейке во многом аналогично с неизменяемым шаблона ячейки.

Во время виртуализации ячейки будут иметь соответствующего контекста привязок обновлена, и поэтому если приложение использует этот режим она должна обеспечивать правильно обрабатывать обновления контекста привязки. Все данные о ячейке должны поступать из контекста привязки или могут возникнуть ошибки согласованности. Это можно сделать с помощью привязки данных для отображения данных ячейки. Кроме того, данные ячейки следует задавать в `OnBindingContextChanged` переопределения, а не в конструкторе пользовательских ячеек, как показано в следующем примере кода:

```csharp
public class CustomCell : ViewCell
{
  Image image = null;

  public CustomCell ()
  {
    image = new Image();
    View = image;
  }

  protected override void OnBindingContextChanged ()
  {
    base.OnBindingContextChanged ();

    var item = BindingContext as ImageItem;
    if (item != null) {
      image.Source = item.ImageUrl;
    }
  }
}
```

Дополнительные сведения см. в разделе [изменения контекста привязки](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

В iOS и Android Если ячейки используют пользовательские модули подготовки отчетов, их необходимо убедиться, правильно реализован уведомления об изменении свойства. Если ячейки повторно их значения свойства будут меняться при обновлении контекста привязки, доступные ячейки с `PropertyChanged` вызванных событий. Дополнительные сведения см. в разделе [Настройка ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement с DataTemplateSelector

Когда [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) использует [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) установите [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) кэширования стратегия не кэширует `DataTemplate`s. Вместо этого `DataTemplate` выбран для каждого элемента данных в списке.

> [!NOTE]
> [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) Стратегию кэширования имеет необходимое условие, представленные в Xamarin.Forms 2.4, что при [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) будет предложено выбрать [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), каждая из которых `DataTemplate` должен возвращать же [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) типа. Например, если [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) с `DataTemplateSelector` , могут возвращать либо `MyDataTemplateA` (где `MyDataTemplateA` возвращает `ViewCell` типа `MyViewCellA`), или `MyDataTemplateB` (где `MyDataTemplateB`возвращает `ViewCell` типа `MyViewCellB`), когда `MyDataTemplateA` возвращается, она должна вернуть `MyViewCellA` или будет вызвано исключение.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) Стратегию кэширования лежит [ `RecycleElement` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElement/) стратегия кэширования, гарантируя, кроме того, что при [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) использует [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) установите [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), `DataTemplate`s кэшируются на тип элемента в списке. Таким образом `DataTemplate`s выбрать один раз на тип элемента, а не один раз для каждого экземпляра элемента.

> [!NOTE]
> [ `RecycleElementAndDataTemplate` ](https://developer.xamarin.com/api/field/Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate/) Стратегию кэширования имеет необходимое условие, `DataTemplate`, возвращаемый [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) необходимо использовать [ `DataTemplate` ](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) конструктор, принимающий `Type`.

### <a name="setting-the-caching-strategy"></a>Установка стратегии кэширования

[ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) Указано значение перечисления с [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) перегрузку конструктора, как показано в следующем примере кода:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

В XAML задайте `CachingStrategy` атрибута, как показано в следующем коде:

```xaml
<ListView CachingStrategy="RecycleElement">
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
              ...
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Это действует так же, как и настройка аргумент стратегии кэширования в конструкторе в C#; Следует отметить, что не `CachingStrategy` свойство `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Установка стратегии кэширования в подклассов ListView

Установка `CachingStrategy` атрибута из XAML подклассов [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) не дает требуемого поведения, так как не `CachingStrategy` свойство `ListView`. Кроме того Если [XAMLC](~/xamarin-forms/xaml/xamlc.md) — включен, будет произведено следующее сообщение об ошибке: **не свойство, связываемое свойство или событие, найти для «CachingStrategy»**

Решение этой проблемы является указание конструктор подклассов [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) , принимающий [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) параметра и передает его в базовом классе:

```csharp
public class CustomListView : ListView
{
  public CustomListView (ListViewCachingStrategy strategy) : base (strategy)
  {
  }
  ...
}
```

Затем [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) можно указать значение перечисления из XAML с помощью `x:Arguments` синтаксис:

```xaml
<local:CustomListView>
  <x:Arguments>
    <ListViewCachingStrategy>RecycleElement</ListViewCachingStrategy>
  </x:Arguments>
</local:CustomListView>
```

<a name="improving-performance" />

## <a name="improving-listview-performance"></a>Повышение производительности ListView

Существует множество методов для повышения производительности `ListView`:

-  Привязать `ItemsSource` свойства `IList<T>` коллекции вместо `IEnumerable<T>` коллекции, так как `IEnumerable<T>` коллекции, которые не поддерживают произвольный доступ.
-  Используйте встроенные ячейки (например `TextCell`  /  `SwitchCell` ) вместо `ViewCell` каждый раз, когда вы можете.
-  Используйте меньшее число элементов. Рассмотрим для примера с помощью одного `FormattedString` метки, а не несколько меток.
-  Замените `ListView` с `TableView` при отображении неоднородные данные — то есть данных разных типов.
-  Ограничение использования [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/) метод. Если поскольку вызовет перегрузку, он может повлиять на производительность.
-  На Android, не устанавливайте значение `ListView`его видимость разделителя строк или цвет после его создания, поскольку приводит к снижению большой производительности.
-  Не следует изменять макет ячейки, на основе [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Это приводит к большой затраты на макет и инициализации.
-  Избегайте глубоко вложенной структуры иерархии. Используйте `AbsoluteLayout` или `Grid` с целью сокращения вложение.
-  Избегайте конкретных `LayoutOptions` отличный от `Fill` (заливки является cheapest вычислений).
-  Избегайте размещения `ListView` внутри `ScrollView` по следующим причинам:
    - `ListView` Реализует собственную прокрутки.
    - `ListView` Не получит все жесты, как будет обрабатываться родительским `ScrollView`.
    - `ListView` Может представлять настраиваемый верхний и нижний колонтитулы прокручивается вместе с элементами списка, потенциально предложение функциональные возможности, `ScrollView` был использован для. Дополнительные сведения см. [верхние и нижние колонтитулы](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
-  Рекомендуется использовать пользовательское средство отрисовки, если требуется специфичны сложных Дизайн, представленных в ячейках.

`AbsoluteLayout` имеет возможность выполнять макеты без вызова одной из них. Это делает очень мощный для повышения производительности. Если `AbsoluteLayout` не может быть используется, рассмотрите возможность [ `RelativeLayout` ](http://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/). При использовании `RelativeLayout`, непосредственно передача ограничения будет намного быстрее, чем использование выражения API. Это, так как выражение API использует JIT и на iOS дереве следует интерпретировать которого снижается. Выражение API подходит для макеты страниц, в которых он требуется только на начального макета и поворота, но в `ListView`, в котором он выполняется непрерывно, при прокрутке, ее производительность может снижаться.

Создание пользовательского средства визуализации для [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) или его ячеек является одним из способов уменьшить эффект вычисления макета на прокрутки производительности. Дополнительные сведения см. в разделе [Настройка ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) и [Настройка ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Связанные ссылки

- [Пользовательское представление модуля подготовки отчетов (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [Пользовательский модуль подготовки отчетов ViewCell (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
- [ListViewCachingStrategy](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)
