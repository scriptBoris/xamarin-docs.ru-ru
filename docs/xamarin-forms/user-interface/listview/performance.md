---
title: Производительность элемента управления ListView
description: Несмотря на то, что ListView является мощным представление для отображения данных, он имеет некоторые ограничения. В этой статье объясняется, как обеспечить высокую производительность с ListView Xamarin.Forms в приложении.
ms.prod: xamarin
ms.assetid: 1B085639-652C-4862-86EB-5D55D32B9395
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2017
ms.openlocfilehash: 906fd60954b18064467e665295dba8bb75ed5a45
ms.sourcegitcommit: 3e980fbf92c69c3dd737554e8c6d5b94cf69ee3a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935593"
---
# <a name="listview-performance"></a>Производительность элемента управления ListView

При написании приложений для мобильных устройств, производительность имеет значение. Пользователи привыкли плавную прокрутку и время быстрой загрузки. Несоответствие требованиям ожиданиям пользователей будет стоить вам оценки в хранилище приложения или в случае с бизнес-приложением издержек время и деньги.

Несмотря на то что [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) — это полезное представление для отображения данных, он имеет некоторые ограничения. Производительность прокрутки может заметно снижаться при использовании пользовательских ячеек, особенно в том случае, если они содержат представлений с глубоким иерархий или использовать определенные макеты, которые требуют значительных измерения. К счастью существуют методы, которые можно использовать, чтобы избежать снижения производительности.

<a name="cachingstrategy" />

## <a name="caching-strategy"></a>Стратегии кэширования

ListViews часто используются для отображения данных гораздо больше, чем можно вписать на экране. Например, рассмотрим приложение music. Библиотека песен возможно, тысяч вхождений. Простой подход, который следует создать строку для каждой песни, бы к снижению производительности. Этот подход впустую экономии памяти и могут замедлить прокрутка для сканирования. Другой подход заключается в том, чтобы создавать и уничтожать строк как данных в области просмотра. Требуется постоянное при создании экземпляра и очистки объектов представления, которые могут быть очень медленно.

Для экономии памяти, собственного [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) эквиваленты для каждой платформы имеют встроенные функции для повторного использования строк. Только ячейки, которые отображаются на экране, загруженные в память и **содержимого** загружается в существующие ячейки. Это дает приложению от необходимости создания экземпляра тысячи объектов, экономя время и память.

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
> Игнорирует универсальной платформы Windows (UWP) [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) стратегию, кэширования, так как он всегда использует кэширование для повышения производительности. Таким образом, по умолчанию он ведет себя так, как если [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) применяется стратегию кэширования.

### <a name="retainelement"></a>RetainElement

[ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) Стратегию кэширования указывает, что [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) создаст ячейку для каждого элемента в списке, и значение по умолчанию `ListView` поведение. Как правило, его следует использовать в следующих случаях:

- Когда каждая ячейка имеет большое количество привязок (20-30 и более).
- Когда шаблон ячейки часто изменяется.
- Если тестирование показывает, что `RecycleElement` кэширования результатов стратегии в снижение быстродействия.

Очень важно для распознавания последствия [ `RetainElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RetainElement) стратегию кэширования, при работе с пользовательские ячейки. Код для инициализации ячейки будет нужно запускать для создания каждой ячейки, которое может иметь несколько раз в секунду. В этом случае макет методики, которые были правильно на странице, такие как с помощью нескольких вложенных [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) экземпляра, становятся узких мест производительности, когда они настроены и уничтожается в режиме реального времени прокрутке пользователем.

<a name="recycleelement" />

### <a name="recycleelement"></a>RecycleElement

[ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) Стратегию кэширования указывает, что [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) будет старайтесь свести к минимуму его памяти объема и скорости выполнения путем перезапуска ячейки списка. Этот режим не всегда обеспечивает повышение производительности и тестирование следует провести, чтобы определить, какие-либо улучшения. Тем не менее он обычно является предпочтительным вариантом и следует использовать в следующих случаях:

- Когда каждая ячейка имеет небольших и умеренное число привязок.
- При каждой ячейки [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/) определяет все ячейки данных.
- При каждой ячейки схожее с неизменяемым шаблона ячеек.

Во время виртуализации ячейка будет иметь контекст привязки обновлены и поэтому если приложение использует этот режим его необходимо убедиться, правильно обрабатывать обновления контекста привязки. Все данные о ячейке должны поступать из контекста привязки, или, возможно возникновение ошибок согласованности. Это можно сделать с помощью привязки данных для отображения данных ячейки. Кроме того, данные ячейки следует задавать в `OnBindingContextChanged` переопределить, а не в конструктор пользовательской ячейки, как показано в следующем примере кода:

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

Дополнительные сведения см. в разделе [изменений контекста привязки](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md#binding-context-changes).

В iOS и Android Если ячейки использовать пользовательские модули подготовки отчетов, они необходимо убедиться, что правильно реализуется уведомления об изменении свойства. При повторно ячеек используются значения свойств изменится при обновлении контекста привязки, доступные ячейки, с помощью `PropertyChanged` вызванных событий. Дополнительные сведения см. в разделе [Настройка ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).

#### <a name="recycleelement-with-a-datatemplateselector"></a>RecycleElement с DataTemplateSelector

Когда [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) использует [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) для выбора [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) кэширования стратегия не кэширует `DataTemplate`s. Вместо этого `DataTemplate` выбран для каждого элемента данных в списке.

> [!NOTE]
> [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) Стратегию кэширования имеет обязательной, представленные в Xamarin.Forms 2.4, что при [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) предлагается выбрать [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), в которых `DataTemplate` должен возвращать же [ `ViewCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ViewCell/) типа. Например, если [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) с `DataTemplateSelector` , могут возвращать либо `MyDataTemplateA` (где `MyDataTemplateA` возвращает `ViewCell` типа `MyViewCellA`), или `MyDataTemplateB` (где `MyDataTemplateB`возвращает `ViewCell` типа `MyViewCellB`), когда `MyDataTemplateA` возвращается, он должен возвращать `MyViewCellA` или будет вызвано исключение.

### <a name="recycleelementanddatatemplate"></a>RecycleElementAndDataTemplate

[ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) Стратегию кэширования лежит [ `RecycleElement` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElement) стратегию кэширования, гарантируя, кроме того, что при [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) использует [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) для выбора [ `DataTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplate/), `DataTemplate`s кэшируемых тип элемента в списке. Таким образом `DataTemplate`s выбраны один раз на тип элемента, а не один раз для каждого экземпляра элемента.

> [!NOTE]
> [ `RecycleElementAndDataTemplate` ](xref:Xamarin.Forms.ListViewCachingStrategy.RecycleElementAndDataTemplate) Стратегию кэширования имеет необходимым условием, `DataTemplate`, возвращаемый [ `DataTemplateSelector` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTemplateSelector/) необходимо использовать [ `DataTemplate` ](https://developer.xamarin.com/api/constructor/Xamarin.Forms.DataTemplate.DataTemplate/p/System.Type/) конструктор, принимающий `Type`.

### <a name="setting-the-caching-strategy"></a>Настройка кэширования стратегии

[ `ListViewCachingStrategy` ](xref:Xamarin.Forms.ListViewCachingStrategy) Указано значение перечисления с [ `ListView` ](xref:Xamarin.Forms.ListView) перегрузку конструктора, как показано в следующем примере кода:

```csharp
var listView = new ListView(ListViewCachingStrategy.RecycleElement);
```

В XAML, задайте `CachingStrategy` атрибута, как показано в следующем коде:

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

Это действует так же, как и Настройка кэширования стратегии аргумента в конструктор в C#; Следует отметить, что не `CachingStrategy` свойство `ListView`.

#### <a name="setting-the-caching-strategy-in-a-subclassed-listview"></a>Настройка кэширования стратегии в подклассах ListView

Установка `CachingStrategy` атрибута из XAML в подклассах [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) не даст требуемое поведение, так как не `CachingStrategy` свойство `ListView`. Кроме того Если [XAMLC](~/xamarin-forms/xaml/xamlc.md) — включена, будет произведено следующее сообщение об ошибке: **не свойство, может быть привязано или событие найдено для «CachingStrategy»**

Решение этой проблемы является указание конструктор для подкласса [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) , принимающий [ `ListViewCachingStrategy` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/) параметра и передает его в базовый класс:

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

Существует множество методов повышения производительности `ListView`:

-  Привязать `ItemsSource` свойства `IList<T>` , а не `IEnumerable<T>` коллекции, так как `IEnumerable<T>` коллекции, которые не поддерживают произвольного доступа.
-  Используйте встроенные ячейки (например `TextCell`  /  `SwitchCell` ) вместо `ViewCell` каждый раз, когда вы можете.
-  Используйте меньшее число элементов. Для примера рассмотрите возможность использования одного `FormattedString` метки, а не несколько меток.
-  Замените `ListView` с `TableView` при отображении неоднородные данные – то есть разные типы данных.
-  Ограничить использование [ `Cell.ForceUpdateSize` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Cell.ForceUpdateSize()/) метод. Поскольку вызовет перегрузку, он снизится производительность.
-  В Android, старайтесь не использовать параметр `ListView`в строку разделителя видимость или цвет после он был создан экземпляр, поскольку приводит к снижению производительности.
-  Не следует изменять макет ячейки, на основе [ `BindingContext` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindableObject.BindingContext/). Это влечет за собой большие затраты макет и инициализации.
-  Избегайте иерархий глубоко вложенных макетов. Используйте `AbsoluteLayout` или `Grid` с целью сокращения вложения.
-  Избежать определенных `LayoutOptions` отличное от `Fill` (заливки — дешевая для вычисления).
-  Избегайте размещения `ListView` внутри `ScrollView` по следующим причинам:
    - `ListView` Реализует свой собственный прокрутки.
    - `ListView` Не получит все жесты, как будут обрабатываться в родительском `ScrollView`.
    - `ListView` Может создать настраиваемый верхний и нижний колонтитул, прокручивает элементы списка, потенциально, предоставляющей функциональность `ScrollView` был использован для. Дополнительные сведения см. в разделе [верхние и нижние колонтитулы](~/xamarin-forms/user-interface/listview/customizing-list-appearance.md#Headers_and_Footers).
-  Если вам нужна специальная разработка сложных, представленные в ячейках, рассмотрите возможность пользовательское средство отрисовки.

`AbsoluteLayout` имеет возможность выполнять макеты без вызова одной из них. Это позволяет очень мощный для производительности. Если `AbsoluteLayout` нельзя использовать, рассмотрите возможность [ `RelativeLayout` ](http://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/). При использовании `RelativeLayout`, непосредственно передачи ограничения будут значительно быстрее, чем использование выражения API. Том, что выражение API использует JIT и в iOS дереве интерпретироваться, которое выполняется медленнее. Выражение API подходит для макетов страниц, в котором его требуется только на исходный макет и поворота, но в `ListView`, где он выполняется постоянно во время прокрутки, ее производительность может снижаться.

Создание пользовательского средства визуализации для [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) или ячейках является одним из подходов к уменьшению влияния вычисления макета на производительность прокрутки. Дополнительные сведения см. в разделе [Настройка ListView](~/xamarin-forms/app-fundamentals/custom-renderer/listview.md) и [Настройка ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md).


## <a name="related-links"></a>Связанные ссылки

- [Пользовательское представление модуля подготовки отчетов (пример)](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithListviewNative/)
- [ViewCell пользовательского модуля подготовки отчетов (пример)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/viewcell/)
- [ListViewCachingStrategy](https://developer.xamarin.com/api/type/Xamarin.Forms.ListViewCachingStrategy/)
