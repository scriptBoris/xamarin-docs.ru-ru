---
title: Создание пользовательского макета
description: В этой статье объясняется, как создать класс пользовательского макета, а также демонстрирует класс WrapLayout, с учетом ориентации, который упорядочивает его дочерние элементы горизонтально по странице и затем создает оболочку для отображения последующих дочерних элементов для дополнительных строк.
ms.prod: xamarin
ms.assetid: B0CFDB59-14E5-49E9-965A-3DCCEDAC2E31
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/29/2017
ms.openlocfilehash: a1027b1fd738c80cf5917effc66957f77a337ecf
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53054571"
---
# <a name="creating-a-custom-layout"></a>Создание пользовательского макета

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/CustomLayout/WrapLayout/)

_Xamarin.Forms определяет четыре класса макета — StackLayout, AbsoluteLayout, RelativeLayout и сетки, и каждый упорядочивает его дочерние элементы по-разному. Тем не менее иногда это необходимо для организации содержимого страницы, с использованием макета, не поддерживаемых Xamarin.Forms. В этой статье объясняется, как создать класс пользовательского макета, а также демонстрирует класс WrapLayout, с учетом ориентации, который упорядочивает его дочерние элементы горизонтально по странице и затем создает оболочку для отображения последующих дочерних элементов для дополнительных строк._

## <a name="overview"></a>Обзор

В Xamarin.Forms, все макета классы являются производными от [ `Layout<T>` ](xref:Xamarin.Forms.Layout`1) класса и ограничение универсального типа, [ `View` ](xref:Xamarin.Forms.View) и его производные типы. В свою очередь `Layout<T>` класс является производным от [ `Layout` ](xref:Xamarin.Forms.Layout) класс, который предоставляет механизм для определения положения и размера дочерних элементов.

Все визуальные элементы отвечает за определение свой собственный предпочтительный размер, который известен как *запрошенный* размер. [`Page`](xref:Xamarin.Forms.Page), [ `Layout` ](xref:Xamarin.Forms.Layout), и [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) производных типов несете ответственность за определение, расположение и размер дочернего, или дочерние элементы, по отношению к себе. Иными словами, макет протоколируется связь родитель потомок, где родительский определяет размер своих дочерних элементов, которые должны быть, но будет пытаться запрошенного размера дочернего элемента.

Исчерпывающие сведения о Xamarin.Forms макет и недействительности циклов необходима для создания пользовательского макета. Теперь будут рассмотрены эти циклы.

## <a name="layout"></a>Макет

Макет начинается в верхней части визуального дерева со страницей, и проходит все ветви визуального дерева вплоть до охватывающих все визуальные элементы на странице. Для изменения размеров и положения их дочерние элементы относительно сами отвечают элементы, которые являются родительскими для других элементов.

[ `VisualElement` ](xref:Xamarin.Forms.VisualElement) Класс определяет [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) метод, который измеряет элемент для операций структуры и [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) метод, который указывает Прямоугольная область элемент будет визуализирован в. При запуске приложения и отображается первая страница, *цикла разметки* первый состоящий из `Measure` вызовы, а затем `Layout` вызывает, начинается на [ `Page` ](xref:Xamarin.Forms.Page) объекта:

1. Во время цикла макета, каждый родительский элемент отвечает за вызов метода `Measure` метод своим дочерним элементам.
1. После потомки были измерены, каждый родительский элемент отвечает за вызов метода `Layout` метод своим дочерним элементам.

Этот цикл гарантирует, что все визуальные элементы на странице получает вызовы `Measure` и `Layout` методы. Этот процесс показан на следующей схеме:

![](custom-images/layout-cycle.png "Цикл макета Xamarin.Forms")

> [!NOTE]
> Обратите внимание на то, что циклы макета также может возникать на подмножестве визуального дерева при каких-либо изменений влияет на макет. Сюда входят элементы, в результате добавления или удаления из коллекции, например, в [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), изменения в [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) свойства элемента или изменении размера элемента.

Каждый класс Xamarin.Forms с `Content` или `Children` свойство имеет переопределяемый [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) метод. Пользовательский макет классы, производные от [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) необходимо переопределить этот метод и убедиться, что [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) и [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) методы являются вызывается на всех его дочерних элементов, для предоставления пользовательского макета.

Кроме того, каждый класс, производный от [ `Layout` ](xref:Xamarin.Forms.Layout) или [ `Layout<View>` ](xref:Xamarin.Forms.Layout`1) необходимо переопределить [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) метод, который находится там, где класс макета Определяет размер, оно должно быть, делая вызовы к [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) методов из его дочерних элементов.

> [!NOTE]
> Элементы определения их размера, на основе *ограничения*, указывающие, какой объем пространства, доступного элемент внутри родительского элемента. Ограничения, передаваемый [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) и [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) методы могут варьироваться от 0 до `Double.PositiveInfinity`. Элемент *ограниченного*, или *полностью ограничить*, при получении вызова его [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) метод с аргументами бессрочной - ограничен элемент определенный размер. Элемент *неограниченного*, или *частично ограниченное*, при получении вызова его `Measure` метод с хотя бы один аргумент, равным `Double.PositiveInfinity` — бесконечное ограничение может быть считать, указывающее, автоматическое изменение размера.

## <a name="invalidation"></a>Недействительность

Недействительности — это процесс, по которому изменение элемента на странице запускает новый цикл макета. Элементы считаются недопустимыми, если они больше не имеют правильный размер или положение. Например если [ `FontSize` ](xref:Xamarin.Forms.Button.FontSize) свойство [ `Button` ](xref:Xamarin.Forms.Button) изменения, `Button` считается недействительным, так как он больше не будет иметь правильный размер. Изменение размера `Button` у окажет влияние изменений в макет через оставшуюся часть страницы.

Элементы недействительным сами путем вызова [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) метод, обычно когда элемента изменяется свойство, может привести к новый размер элемента. Этот метод запускает [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) событие, которое обрабатывает родительского элемента, чтобы активировать новый цикл макета.

[ `Layout` ](xref:Xamarin.Forms.Layout) Классе задает обработчик для [ `MeasureInvalidated` ](xref:Xamarin.Forms.VisualElement.MeasureInvalidated) событий на всех дочерних действиях, добавить его `Content` свойство или `Children` коллекции и отсоединяет обработчик при дочерние удаляется. Таким образом каждый элемент в визуальном дереве, имеющий дочерние элементы оповещения при каждом изменении размер одного из его дочерних элементов. На следующей схеме показана, как изменение размера элемента в визуальном дереве может привести к изменениям, которые ripple вверх по дереву:

![](custom-images/invalidation.png "Недействительности данных в визуальном дереве")

Тем не менее `Layout` классе предпринимается попытка ограничить влияние изменения в размере ребенка на макет страницы. Если макет является ограниченного размера, затем изменение размера дочерних не влияет на все выше, чем родительского макета в визуальном дереве. Тем не менее изменения в размере макета сказывается на том, как макет упорядочивает его дочерние элементы. Таким образом, любое изменение в размер макета будет запущен цикл макета для размещения, и макет будет получать вызовы к его [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) и [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) методы.

[ `Layout` ](xref:Xamarin.Forms.Layout) Класс также определяет [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) метод, который имеет же цель [ `InvalidateMeasure` ](xref:Xamarin.Forms.VisualElement.InvalidateMeasure) метод. `InvalidateLayout` Должен быть вызван метод, каждый раз, когда вносится изменение, влияющее на способ макет размещает и устанавливает размер его дочерних элементов. Например `Layout` класс вызывает `InvalidateLayout` метод всякий раз, когда дочерний элемент является добавляемые или удаляемые из макета.

[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) Может быть переопределен для реализации кэша, чтобы свести к минимуму повторяющихся вызовов [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) методы макета дочерних элементов. Переопределение `InvalidateLayout` метод предоставляет уведомления об при добавлении дочерних элементов или удалены из разметки. Аналогичным образом [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) метод может быть переопределен для предоставления уведомления при изменении размера одного из дочерних элементов макета. Для обоих переопределения методов настраиваемый макет должен вернуть ответ Очистка кэша. Дополнительные сведения см. в разделе [вычисление и кэширование данных](#caching).

## <a name="creating-a-custom-layout"></a>Создание пользовательского макета

Процесс создания пользовательского макета выглядит следующим образом:

1. Создайте класс, производный от класса `Layout<View>`. Дополнительные сведения см. в разделе [Создание WrapLayout](#creating).
1. [*необязательно*] добавить свойства, поддерживаемые свойства для привязки для любых параметров, которые следует задать для класса макета. Дополнительные сведения см. в разделе [Добавление свойства поддерживаемый привязываемые свойства](#adding_properties).
1. Переопределить [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) вызываемого метода [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) метод на дочерние элементы макета и возвращают запрошенный размер для макета. Дополнительные сведения см. в разделе [переопределение метода OnMeasure](#onmeasure).
1. Переопределить [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) вызываемого метода [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) метод макета дочерних элементов. Ошибка вызова [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) метод на каждый дочерний элемент в макете вызовет никогда не получает требуемый размер или положение дочерних, и поэтому дочерних, не станет видимым на странице. Дополнительные сведения см. в разделе [переопределение метода LayoutChildren](#layoutchildren).

  > [!NOTE]
>  При перечислении дочерних элементов в [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) и [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) переопределений, пропустить любой дочерний которого [ `IsVisible` ](xref:Xamarin.Forms.VisualElement.IsVisible) свойству `false`. Это позволит гарантировать, что пользовательский макет не оставить место для невидимые дочерние элементы.

1. [*необязательно*] переопределение [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) метод, чтобы получать уведомления при добавлении дочерних элементов или удалены из разметки. Дополнительные сведения см. в разделе [переопределение метода InvalidateLayout](#invalidatelayout).
1. [*необязательно*] переопределение [ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) метод, чтобы получать уведомления при изменении размера одного из дочерних элементов макета. Дополнительные сведения см. в разделе [переопределение метода OnChildMeasureInvalidated](#onchildmeasureinvalidated).

> [!NOTE]
> Обратите внимание, что [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) переопределение не будут вызваны, если размер макета управляется родительским, а не его дочерние элементы. Тем не менее, если один или оба ограничения являются бесконечными или класс макета имеет не по умолчанию будет вызываться переопределение [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) или [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) значения свойств. По этой причине [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) переопределения нельзя полагаться на размеры дочерних, полученные во время [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) вызова метода. Вместо этого `LayoutChildren` необходимо вызвать [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) метод для расположения дочерних элементов, перед вызовом [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) метод. Кроме того, получить размер дочерних элементов в `OnMeasure` переопределение могут кэшироваться, чтобы впоследствии избежать `Measure` вызовов в `LayoutChildren` переопределение, но класс макета необходимо знать, когда необходимо снова получить размеры. Дополнительные сведения см. в разделе [вычисление и кэширование данных макета](#caching).

Затем макет класса можно использовать путем добавления его в [ `Page` ](xref:Xamarin.Forms.Page), а также посредством добавления дочерних элементов в макет. Дополнительные сведения см. в разделе [использование WrapLayout](#consuming).

<a name="creating" />

### <a name="creating-a-wraplayout"></a>Создание WrapLayout

Пример приложения демонстрирует учетом ориентации `WrapLayout` класс, который упорядочивает его дочерние элементы горизонтально по странице и затем создает оболочку для отображения последующих дочерних элементов для дополнительных строк.

`WrapLayout` Класс выделяет такой же объем пространства для каждого дочернего, известный как *размеру ячейки*, основываясь на максимальный размер дочерних элементов. Дочерние элементы, меньше, чем размер ячейки, которые могут размещаться в ячейке на основе их [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions) значения свойств.

`WrapLayout` Определение класса показано в следующем примере кода:

```csharp
public class WrapLayout : Layout<View>
{
  Dictionary<Size, LayoutData> layoutDataCache = new Dictionary<Size, LayoutData>();
  ...
}
```

<a name="caching" />

#### <a name="calculating-and-caching-layout-data"></a>Вычисление и кэширование данных макета

`LayoutData` Структура сохраняет данные о коллекции дочерних элементов в ряд свойств:

- `VisibleChildCount` — число дочерних объектов, видимых в макете.
- `CellSize` — Максимальный размер всех дочерних элементов, размером макета.
- `Rows` — количество строк.
- `Columns` — количество столбцов.

`layoutDataCache` Поле используется для хранения нескольких `LayoutData` значения. При запуске приложения, два `LayoutData` объекты будут кэшироваться в `layoutDataCache` словарь для текущей ориентации — один для ограничения аргументов `OnMeasure` переопределение и один для `width` и `height` аргументы Чтобы `LayoutChildren` переопределить. При смене устройства в альбомной ориентации `OnMeasure` переопределить и `LayoutChildren` переопределение будет снова вызван, приведет к другой двух `LayoutData` объекты, кэширование в словарь. Тем не менее, когда устройство возвращается в книжной ориентации, дальнейшие вычисления не необходимы, так как `layoutDataCache` уже содержит необходимые данные.

В следующем коде показано в примере `GetLayoutData` метод, который рассчитывает свойства `LayoutData` структурированных на основе определенного размера:

```csharp
LayoutData GetLayoutData(double width, double height)
{
  Size size = new Size(width, height);

  // Check if cached information is available.
  if (layoutDataCache.ContainsKey(size))
  {
    return layoutDataCache[size];
  }

  int visibleChildCount = 0;
  Size maxChildSize = new Size();
  int rows = 0;
  int columns = 0;
  LayoutData layoutData = new LayoutData();

  // Enumerate through all the children.
  foreach (View child in Children)
  {
    // Skip invisible children.
    if (!child.IsVisible)
      continue;

    // Count the visible children.
    visibleChildCount++;

    // Get the child's requested size.
    SizeRequest childSizeRequest = child.Measure(Double.PositiveInfinity, Double.PositiveInfinity);

    // Accumulate the maximum child size.
    maxChildSize.Width = Math.Max(maxChildSize.Width, childSizeRequest.Request.Width);
    maxChildSize.Height = Math.Max(maxChildSize.Height, childSizeRequest.Request.Height);
  }

  if (visibleChildCount != 0)
  {
    // Calculate the number of rows and columns.
    if (Double.IsPositiveInfinity(width))
    {
      columns = visibleChildCount;
      rows = 1;
    }
    else
    {
      columns = (int)((width + ColumnSpacing) / (maxChildSize.Width + ColumnSpacing));
      columns = Math.Max(1, columns);
      rows = (visibleChildCount + columns - 1) / columns;
    }

    // Now maximize the cell size based on the layout size.
    Size cellSize = new Size();

    if (Double.IsPositiveInfinity(width))
      cellSize.Width = maxChildSize.Width;
    else
      cellSize.Width = (width - ColumnSpacing * (columns - 1)) / columns;

    if (Double.IsPositiveInfinity(height))
      cellSize.Height = maxChildSize.Height;
    else
      cellSize.Height = (height - RowSpacing * (rows - 1)) / rows;

    layoutData = new LayoutData(visibleChildCount, cellSize, rows, columns);
  }

  layoutDataCache.Add(size, layoutData);
  return layoutData;
}
```

`GetLayoutData` Метод выполняет следующие операции:

- Он определяет, справедливо ли вычисляемый `LayoutData` значение уже находится в кэше и возвращает его, если он доступен.
- В противном случае он перечисляет по всем дочерним узлам, вызов `Measure` метод на каждом дочернем с неограниченной ширины и высоты и определяет размер максимального дочерних.
- При условии, что имеется по крайней мере один видимого дочернего элемента, он вычисляет количество строк и столбцов, необходимых, а затем вычисляет размер ячейки для дочерних элементов, в зависимости от размеров `WrapLayout`. Обратите внимание, что размер ячейки обычно немного шире, чем дочерних максимальный размер, но что это также может быть меньшего размера Если `WrapLayout` не достаточно широкий для широкой дочерний или высоту достаточно для самого высокого дочернего элемента.
- Она сохраняет новый `LayoutData` значение в кэше.

<a name="adding_properties" />

#### <a name="adding-properties-backed-by-bindable-properties"></a>Добавление свойств, поддерживаемый привязываемые свойства

`WrapLayout` Класс определяет `ColumnSpacing` и `RowSpacing` свойства, значения которого используются для разделения строк и столбцов в макете, и который обеспечиваются связываемые свойства. Привязываемые свойства показаны в следующем примере кода:

```csharp
public static readonly BindableProperty ColumnSpacingProperty = BindableProperty.Create(
  "ColumnSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });

public static readonly BindableProperty RowSpacingProperty = BindableProperty.Create(
  "RowSpacing",
  typeof(double),
  typeof(WrapLayout),
  5.0,
  propertyChanged: (bindable, oldvalue, newvalue) =>
  {
    ((WrapLayout)bindable).InvalidateLayout();
  });
```

Вызывает обработчиком property-changed каждого привязываемые свойства `InvalidateLayout` сдадите переопределяющий метод, чтобы активировать новый макет `WrapLayout`. Дополнительные сведения см. в разделе [переопределение метода InvalidateLayout](#invalidatelayout) и [переопределение метода OnChildMeasureInvalidated](#onchildmeasureinvalidated).

<a name="onmeasure" />

#### <a name="overriding-the-onmeasure-method"></a>Переопределение метода OnMeasure

`OnMeasure` Переопределение показан в следующем примере кода:

```csharp
protected override SizeRequest OnMeasure(double widthConstraint, double heightConstraint)
{
  LayoutData layoutData = GetLayoutData(widthConstraint, heightConstraint);
  if (layoutData.VisibleChildCount == 0)
  {
    return new SizeRequest();
  }

  Size totalSize = new Size(layoutData.CellSize.Width * layoutData.Columns + ColumnSpacing * (layoutData.Columns - 1),
                layoutData.CellSize.Height * layoutData.Rows + RowSpacing * (layoutData.Rows - 1));
  return new SizeRequest(totalSize);
}
```

Вызывает переопределение `GetLayoutData` метод и конструкций `SizeRequest` объект из возвращенных данных, а также принимая во внимание `RowSpacing` и `ColumnSpacing` значения свойств. Дополнительные сведения о `GetLayoutData` метод, см. в разделе [вычисление и кэширование данных](#caching).

> [!IMPORTANT]
> [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)) И [ `OnMeasure` ](xref:Xamarin.Forms.VisualElement.OnMeasure(System.Double,System.Double)) методы никогда не следует запрашивать бесконечный измерения, возвращая [ `SizeRequest` ](xref:Xamarin.Forms.SizeRequest) значение со свойством, имеющим значение `Double.PositiveInfinity`. Тем не менее хотя бы один из аргументов ограничения для `OnMeasure` может быть `Double.PositiveInfinity`.

<a name="layoutchildren" />

#### <a name="overriding-the-layoutchildren-method"></a>Переопределение метода LayoutChildren

`LayoutChildren` Переопределение показан в следующем примере кода:

```csharp
protected override void LayoutChildren(double x, double y, double width, double height)
{
  LayoutData layoutData = GetLayoutData(width, height);

  if (layoutData.VisibleChildCount == 0)
  {
    return;
  }

  double xChild = x;
  double yChild = y;
  int row = 0;
  int column = 0;

  foreach (View child in Children)
  {
    if (!child.IsVisible)
    {
      continue;
    }

    LayoutChildIntoBoundingRegion(child, new Rectangle(new Point(xChild, yChild), layoutData.CellSize));
    if (++column == layoutData.Columns)
    {
      column = 0;
      row++;
      xChild = x;
      yChild += RowSpacing + layoutData.CellSize.Height;
    }
    else
    {
      xChild += ColumnSpacing + layoutData.CellSize.Width;
    }
  }
}
```

Переопределение начинается с вызова `GetLayoutData` метод, а затем перечисляет все дочерние объекты для их размера и положения в пределах каждого дочернего элемента ячейки. Это достигается путем вызова [ `LayoutChildIntoBoundingRegion` ](xref:Xamarin.Forms.Layout.LayoutChildIntoBoundingRegion(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle)) метод, который используется для расположения дочерних внутри прямоугольника, на основе его [ `HorizontalOptions` ](xref:Xamarin.Forms.View.HorizontalOptions) и [ `VerticalOptions` ](xref:Xamarin.Forms.View.VerticalOptions)значения свойств. Это эквивалентно выполнения звонка в дочернюю [ `Layout` ](xref:Xamarin.Forms.VisualElement.Layout(Xamarin.Forms.Rectangle)) метод.

> [!NOTE]
> Обратите внимание, что прямоугольник, переданный в `LayoutChildIntoBoundingRegion` метод включает всю область, в котором может находиться дочернего.

Дополнительные сведения о `GetLayoutData` метод, см. в разделе [вычисление и кэширование данных](#caching).

<a name="invalidatelayout" />

#### <a name="overriding-the-invalidatelayout-method"></a>Переопределение метода InvalidateLayout

[ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) Переопределение вызывается в том случае, когда дочерние элементы добавлении или удалении из макета, или если в одном из `WrapLayout` значение изменений свойств, как показано в следующем примере кода:

```csharp
protected override void InvalidateLayout()
{
  base.InvalidateLayout();
  layoutInfoCache.Clear();
}
```

Переопределение делает макет недействительным и удаляет все сведения о кэшированных макета.

> [!NOTE]
> Чтобы остановить [ `Layout` ](xref:Xamarin.Forms.Layout) вызова класса [ `InvalidateLayout` ](xref:Xamarin.Forms.Layout.InvalidateLayout) переопределение метода всякий раз, когда дочерний элемент является добавляемые или удаляемые из макета, [ `ShouldInvalidateOnChildAdded` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildAdded(Xamarin.Forms.View)) и [ `ShouldInvalidateOnChildRemoved` ](xref:Xamarin.Forms.Layout.ShouldInvalidateOnChildRemoved(Xamarin.Forms.View)) методов и возврата `false`. Класс макета затем можно реализовать пользовательский процесс, при удалении или добавлении дочерних элементов.

<a name="onchildmeasureinvalidated" />

#### <a name="overriding-the-onchildmeasureinvalidated-method"></a>Переопределение метода OnChildMeasureInvalidated

[ `OnChildMeasureInvalidated` ](xref:Xamarin.Forms.Layout.OnChildMeasureInvalidated) Переопределение вызывается в том случае, когда один из дочерних элементов макета изменяется размер и показано в следующем примере кода:

```csharp
protected override void OnChildMeasureInvalidated()
{
  base.OnChildMeasureInvalidated();
  layoutInfoCache.Clear();
}
```

Переопределение делает недействительной макета дочерних и отменяет все кэшированные макет данных.

<a name="consuming" />

### <a name="consuming-the-wraplayout"></a>Использование WrapLayout

`WrapLayout` Класса могут использоваться, поместив его на [ `Page` ](xref:Xamarin.Forms.Page) производный тип, как показано в следующем примере кода XAML:

```xaml
<ContentPage ... xmlns:local="clr-namespace:ImageWrapLayout">
    <ScrollView Margin="0,20,0,20">
        <local:WrapLayout x:Name="wrapLayout" />
    </ScrollView>
</ContentPage>
```

Ниже приведен эквивалентный код на C#:

```csharp
public class ImageWrapLayoutPageCS : ContentPage
{
  WrapLayout wrapLayout;

  public ImageWrapLayoutPageCS()
  {
    wrapLayout = new WrapLayout();

    Content = new ScrollView
    {
      Margin = new Thickness(0, 20, 0, 20),
      Content = wrapLayout
    };
  }
  ...
}
```

Затем можно добавить дочерние элементы к `WrapLayout` при необходимости. В следующем коде показано в примере [ `Image` ](xref:Xamarin.Forms.Image) элементы, добавляемые `WrapLayout`:

```csharp
protected override async void OnAppearing()
{
  base.OnAppearing();

  var images = await GetImageListAsync();
  foreach (var photo in images.Photos)
  {
    var image = new Image
    {
      Source = ImageSource.FromUri(new Uri(photo + string.Format("?width={0}&height={0}&mode=max", Device.RuntimePlatform == Device.UWP ? 120 : 240)))
    };
    wrapLayout.Children.Add(image);
  }
}

async Task<ImageList> GetImageListAsync()
{
  var requestUri = "https://docs.xamarin.com/demo/stock.json";
  using (var client = new HttpClient())
  {
    var result = await client.GetStringAsync(requestUri);
    return JsonConvert.DeserializeObject<ImageList>(result);
  }
}
```

Если на страницу, содержащую `WrapLayout` появится, образец приложения обращается к удаленной JSON-файл, содержащий список фотографий, асинхронно создает [ `Image` ](xref:Xamarin.Forms.Image) фото для каждого элемента и добавляет его `WrapLayout`. Это приводит к появлению, показано на следующем снимке экрана:

![](custom-images/portait-screenshots.png "Снимки экрана из образца приложения книжной ориентации")

Ниже показаны снимки экрана `WrapLayout` после поворачивается на альбомную ориентацию:

![](custom-images/landscape-ios.png "Пример iOS снимок экрана альбомной ориентации приложения")
![](custom-images/landscape-android.png "Android приложения альбомная снимок экрана")
![](custom-images/landscape-uwp.png " Снимок экрана альбомной ориентации приложения универсальной платформы Windows")

Число столбцов в каждой строке зависит от того, размер фотографии, ширину экрана и количество точек в аппаратно независимая единица. [ `Image` ](xref:Xamarin.Forms.Image) Элементы асинхронная загрузка фотографии и поэтому `WrapLayout` класс получит часто обращаются к его [ `LayoutChildren` ](xref:Xamarin.Forms.Layout.LayoutChildren(System.Double,System.Double,System.Double,System.Double)) метод, так как каждое `Image` элемент получает новый размер, в зависимости от загрузки фотографии.

## <a name="summary"></a>Сводка

В этой статье было рассмотрено, как написать класс пользовательского макета и продемонстрировали учетом ориентации `WrapLayout` класс, который упорядочивает его дочерние элементы горизонтально по странице и затем создает оболочку для отображения последующих дочерних элементов для дополнительных строк.


## <a name="related-links"></a>Связанные ссылки

- [WrapLayout (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/CustomLayout/WrapLayout/)
- [Пользовательские макеты](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter26.md)
- [Создание пользовательских макетов в Xamarin.Forms (видео)](https://evolve.xamarin.com/session/56e20f83bad314273ca4d81c)
- [Макет<T>](xref:Xamarin.Forms.Layout`1)
- [Макет](xref:Xamarin.Forms.Layout)
- [VisualElement](xref:Xamarin.Forms.VisualElement)
