---
title: Производительность Xamarin.Forms
description: Существует множество методов повышения производительности приложений Xamarin.Forms. Вместе они могут значительно снизить загрузку ЦП и сократить объем памяти, используемой приложением. Эти методы описаны в данной статье.
ms.prod: xamarin
ms.assetid: 0be84c56-6698-448d-be5a-b4205f1caa9f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 37d99add473203d90cb1b420536827e34e834a2b
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209327"
---
# <a name="xamarinforms-performance"></a>Производительность Xamarin.Forms

_Существует множество методов повышения производительности приложений Xamarin.Forms. Вместе они могут значительно снизить загрузку ЦП и сократить объем памяти, используемой приложением. Все они описаны в этой статье._

> [!VIDEO https://youtube.com/embed/RZvdql3Ev0E]

**Усовершенствовано в 2016 г.: оптимизация производительности приложения с помощью Xamarin.Forms**

## <a name="overview"></a>Обзор

Низкая производительность приложения проявляется по-разному. Из-за нее приложение может переставать отвечать на запросы, могут возникать задержки при прокрутке и сокращаться время работы батареи. Однако оптимизация производительности предусматривает не только правильную реализацию кода. Необходимо также учитывать эффективность работы пользователей. Например, чтобы повысить удобство работы, необходимо сделать так, чтобы выполнение операций не мешало пользователю выполнять другие действия.

Существует ряд методов повышения производительности приложения Xamarin.Forms, в том числе воспринимаемой пользователем. В их число входят следующее.

- [Включение компилятора XAML](#xamlc)
- [Выбор подходящего макета](#correctlayout)
- [Включение сжатия макета](#layoutcompression)
- [Использование быстрых отрисовщиков](#fastrenderers)
- [Сокращение количества ненужных привязок](#databinding)
- [Оптимизация производительности макета](#optimizelayout)
- [Оптимизация производительности элемента управления ListView](#optimizelistview)
- [Оптимизация графических ресурсов](#optimizeimages)
- [Уменьшение размера визуального дерева](#visualtree)
- [Уменьшение размера словаря ресурсов приложения](#resourcedictionary)
- [Использование шаблона настраиваемого отрисовщика](#rendererpattern)

> [!NOTE]
>  Прежде чем прочитать эту статью, ознакомьтесь со статьей [Кроссплатформенная производительность](~/cross-platform/deploy-test/memory-perf-best-practices.md), в которой описываются универсальные для всех платформ способы оптимизации использования памяти и повышения производительности приложений, построенных с помощью платформы Xamarin.

<a name="xamlc" />

## <a name="enable-the-xaml-compiler"></a>Включение компилятора XAML

При необходимости можно воспользоваться компилятором XAML (XAMLC) и скомпилировать XAML напрямую в промежуточный язык (IL). Компилятор XAMLC предлагает ряд преимуществ, которые перечислены далее:

- Он проверяет XAML во время компиляции, уведомляя пользователя об ошибках.
- Он сокращает часть времени загрузки и создания элементов XAML.
- Он позволяет сократить размер файла окончательной сборки, так как больше не добавляет XAML-файлы.

XAMLC отключен по умолчанию для обеспечения обратной совместимости. Однако его можно включить на уровне класса и сборки. Дополнительные сведения см. в разделе [Компиляция XAML](~/xamarin-forms/xaml/xamlc.md).

<a name="correctlayout" />

## <a name="choose-the-correct-layout"></a>Выбор подходящего макета

Макет, который может отображать несколько дочерних элементов, но имеет лишь один такой элемент, связан с дополнительными издержками. Например, в следующем примере кода показан макет [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) с одним дочерним элементом:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <StackLayout>
            <Image Source="waterfront.jpg" />
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Он нерационален, поэтому элемент [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) должен быть удален, как показано в следующем примере кода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DisplayImage.HomePage">
    <ContentPage.Content>
        <Image Source="waterfront.jpg" />
    </ContentPage.Content>
</ContentPage>
```

Кроме того, не пытайтесь воспроизвести внешний вид определенного макета с помощью сочетаний других макетов, так как это приводит к выполнению ненужных вычислений макета. Например, не пытайтесь воспроизвести макет [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) с помощью сочетания экземпляров [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/). В следующем коде показан пример такого нерекомендуемого действия:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Name:" />
                <Entry Placeholder="Enter your name" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Age:" />
                <Entry Placeholder="Enter your age" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Occupation:" />
                <Entry Placeholder="Enter your occupation" />
            </StackLayout>
            <StackLayout Orientation="Horizontal">
                <Label Text="Address:" />
                <Entry Placeholder="Enter your address" />
            </StackLayout>
        </StackLayout>
    </ContentPage.Content>
</ContentPage>
```

Такой метод неэффективен, так как выполняются ненужные вычисления макета. Вместо этого для формирования нужного макета следует использовать [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/), как показано в следующем примере кода:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Details.HomePage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="100" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
                <RowDefinition Height="30" />
            </Grid.RowDefinitions>
            <Label Text="Name:" />
            <Entry Grid.Column="1" Placeholder="Enter your name" />
            <Label Grid.Row="1" Text="Age:" />
            <Entry Grid.Row="1" Grid.Column="1" Placeholder="Enter your age" />
            <Label Grid.Row="2" Text="Occupation:" />
            <Entry Grid.Row="2" Grid.Column="1" Placeholder="Enter your occupation" />
            <Label Grid.Row="3" Text="Address:" />
            <Entry Grid.Row="3" Grid.Column="1" Placeholder="Enter your address" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

<a name="layoutcompression" />

## <a name="enable-layout-compression"></a>Включение сжатия макета

Сжатие макета позволяет удалить указанные макеты из визуального дерева в целях повышения скорости отрисовки страниц. В этом случае рост производительности зависит от сложности страницы, версии используемой операционной системы и устройства, на котором выполняется приложение. Однако наиболее заметное повышение производительности будет наблюдаться на старых устройствах. Дополнительные сведения см. в статье [Сжатие макета](~/xamarin-forms/user-interface/layouts/layout-compression.md).

<a name="fastrenderers" />

## <a name="use-fast-renderers"></a>Использование быстрых отрисовщиков

Быстрые отрисовщики сокращают затраты на отрисовку элементов управления Xamarin.Forms на платформе Android путем сведения итоговой иерархии собственных элементов управления. Это еще больше способствует повышению производительности, так как создается меньше объектов, в результате чего формируется менее сложное визуальное дерево, а также сокращается использование памяти. Дополнительные сведения см. в статье [Быстрые отрисовщики](~/xamarin-forms/internals/fast-renderers.md).

<a name="databinding" />

## <a name="reduce-unnecessary-bindings"></a>Сокращение количества ненужных привязок

Не используйте привязки для содержимого, которое можно легко задать статически. В привязке данных, которые не должны быть привязаны, нет никакой выгоды, так как привязки нерентабельны с точки зрения затрат. Например, установка `Button.Text = "Accept"` связана с меньшими издержками, чем привязка [`Button.Text`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Text/) к свойству `string` компонента ViewModel со значением "Accept".

<a name="optimizelayout" />

## <a name="optimize-layout-performance"></a>Оптимизация производительности макета

В Xamarin.Forms 2 появился оптимизированный обработчик макета, который влияет на обновления макета. Чтобы добиться максимально возможной производительности, соблюдайте следующие правила:

- Сократите глубину иерархий макетов, указав значения свойств [`Margin`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.Margin/), что позволит создавать макеты с меньшим количеством представлений-оболочек. Дополнительные сведения см. в статье [Внешние и внутренние поля](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).
- При использовании [`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) постарайтесь сделать так, чтобы размер [`Auto`](https://developer.xamarin.com/api/property/Xamarin.Forms.GridLength.Auto/) был задан как можно меньшему количеству строк и столбцов. Из-за каждой строки или столбца с автоматическим размером обработчик макета будет выполнять дополнительные вычисления макета. Если возможно, используйте строки и столбцы фиксированного размера. Кроме того, с помощью значения перечисления [`GridUnitType.Star`](https://developer.xamarin.com/api/field/Xamarin.Forms.GridUnitType.Star/) можно указать, чтобы строки и столбцы занимали пропорциональную часть пространства при условии, что родительское дерево соответствует этим правилам макета.
- Не задавайте свойства макета [`VerticalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/) и [`HorizontalOptions`](https://developer.xamarin.com/api/property/Xamarin.Forms.View.VerticalOptions/), если этого не требуется. Значения по умолчанию для свойств [`LayoutOptions.Fill`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.Fill/) и [`LayoutOptions.FillAndExpand`](https://developer.xamarin.com/api/field/Xamarin.Forms.LayoutOptions.FillAndExpand/) обеспечивают наилучшую оптимизацию макета. Изменение этих свойств связано с издержками и потреблением памяти даже в том случае, если свойствам задаются значения по умолчанию.
- По возможности избегайте использования [`RelativeLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.RelativeLayout/). В противном случае ЦП будет испытывать значительно большую нагрузку.
- При использовании [`AbsoluteLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.AbsoluteLayout/) по возможности избегайте использования свойства [`AbsoluteLayout.AutoSize`](https://developer.xamarin.com/api/property/Xamarin.Forms.AbsoluteLayout.AutoSize/).
- При использовании [`StackLayout`](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) убедитесь, что свойство [`LayoutOptions.Expands`](https://developer.xamarin.com/api/property/Xamarin.Forms.LayoutOptions.Expands/) задано только одному дочернему элементу. В этом случае указанный дочерний элемент будет занимать максимальное пространство, предоставляемое ему макетом `StackLayout`. Выполнять эти вычисления несколько раз слишком затратно.
- Не вызывайте методы класса [`Layout`](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout/), так как они связаны с дорогостоящими вычислениями макета. Скорее всего, нужного поведения макета можно добиться путем установки свойств [`TranslationX`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationX/) и [`TranslationY`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.TranslationY/). Кроме того, для этого можно вывести подкласс из класса [`Layout<View>`](https://developer.xamarin.com/api/type/Xamarin.Forms.Layout%3CT%3E/).
- Не обновляйте экземпляры [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) чаще, чем требуется, так как изменение размера метки может привести к пересчету всего макета экрана.
- Не задавайте свойство [`Label.VerticalTextAlignment`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.VerticalTextAlignment/), если это не требуется.
- По возможности задайте свойству [`LineBreakMode`](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.LineBreakMode/) любых экземпляров [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) значение [`NoWrap`](https://developer.xamarin.com/api/field/Xamarin.Forms.LineBreakMode.NoWrap/).

<a name="optimizelistview" />

## <a name="optimize-listview-performance"></a>Оптимизация производительности элемента управления ListView

При использовании элемента управления [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) необходимо оптимизировать несколько механизмов взаимодействия с пользователем.

- **Инициализация** — интервал времени, который начинается с момента создания элемента управления, и заканчивается при отображении элементов на экране.
- **Прокрутка** — возможность прокрутки списка и проверка того, что пользовательский интерфейс не отстает от сенсорных жестов.
- **Взаимодействие** для добавления, удаления и выбора элементов.

Для использования элемента управления [`ListView`](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) требуется предоставить шаблоны данных и ячеек. От выполнения этого условия будет в значительной степени зависеть производительность элемента управления. Дополнительные сведения см. в статье [Производительность элемента управления ListView](~/xamarin-forms/user-interface/listview/performance.md).

<a name="optimizeimages" />

## <a name="optimize-image-resources"></a>Оптимизация графических ресурсов

Отображение графических ресурсов может значительно увеличивать объем памяти, занимаемой приложением. Поэтому их следует создавать только при необходимости и сразу же освобождать, если они больше не нужны приложению. Например, если приложение отображает изображение, считывая его данные из потока, убедитесь, что поток создается только в том случае, если он необходим. Когда поток больше не нужен, его следует освободить. Это можно сделать, создав поток при создании страницы или при возникновении события [`Page.Appearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Appearing/), а затем удалив его при возникновении события [`Page.Disappearing`](https://developer.xamarin.com/api/event/Xamarin.Forms.Page.Disappearing/).

Изображение, скачанное с помощью метода [`ImageSource.FromUri`](https://developer.xamarin.com/api/member/Xamarin.Forms.ImageSource.FromUri/p/System.Uri/), необходимо кэшировать, задав свойству [`UriImageSource.CachingEnabled`](https://developer.xamarin.com/api/property/Xamarin.Forms.UriImageSource.CachingEnabled/) значение `true`. Дополнительные сведения см. в статье о [работе с изображениями](~/xamarin-forms/user-interface/images.md).

Дополнительные сведения см. в разделе [Оптимизация графических ресурсов](~/cross-platform/deploy-test/memory-perf-best-practices.md#optimizeimages).

<a name="visualtree" />

## <a name="reduce-the-visual-tree-size"></a>Уменьшение размера визуального дерева

Сокращение количества элементов на странице способствует ускорению отрисовки страницы. Существует два основных способа решения этой задачи. Первый заключается в скрытии элементов, которые не отображаются. Свойство [`IsVisible`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.IsVisible/) каждого элемента определяет, должен ли элемент быть частью визуального дерева. Таким образом, если элемент не отображается, поскольку он скрыт другими элементами, удалите элемент или задайте его свойству `IsVisible` значение `false`.

Второй метод предполагает удаление ненужных элементов. Например, в следующем примере кода показан макет страницы, отображающий ряд элементов [`Label`](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/):

```xaml
<ContentPage.Content>
    <StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Hello" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Welcome to the App!" />
        </StackLayout>
        <StackLayout Padding="20,20,0,0">
            <Label Text="Downloading Data..." />
        </StackLayout>
    </StackLayout>
</ContentPage.Content>
```

Этот же макет страницы можно оставить с меньшим количеством элементов, как показано в следующем примере кода:

```xaml
<ContentPage.Content>
  <StackLayout Padding="20,20,0,0" Spacing="25">
    <Label Text="Hello" />
    <Label Text="Welcome to the App!" />
    <Label Text="Downloading Data..." />
  </StackLayout>
</ContentPage.Content>
```

<a name="resourcedictionary" />

## <a name="reduce-the-application-resource-dictionary-size"></a>Уменьшение размера словаря ресурсов приложения

Во избежание дублирования все ресурсы, используемые в приложении, должны храниться в словаре ресурсов приложения. Это позволит сократить объем кода XAML, подлежащий анализу в приложении. В следующем примере кода показан ресурс `HeadingLabelStyle`, который используется во всем приложении и, таким образом, определен в словаре ресурсов приложении:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Resources.App">
     <Application.Resources>
         <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
         </ResourceDictionary>
     </Application.Resources>
</Application>
```

Тем не менее XAML, соответствующий странице, не следует включать в словарь ресурсов приложения, так как анализ ресурсов будет осуществляться при запуске приложения, а не когда это необходимо для страницы. Если ресурс используется страницей, не являющейся стартовой, его необходимо поместить в словарь ресурсов для этой страницы, что способствует сокращению объема XAML, анализируемого при запуске приложения. В следующем примере кода показан ресурс `HeadingLabelStyle`, который используется только на одной странице и, таким образом, определен в словаре ресурсов страницы:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Test.HomePage"
             Padding="0,20,0,0">
     <ContentPage.Resources>
          <ResourceDictionary>
            <Style x:Key="HeadingLabelStyle" TargetType="Label">
                <Setter Property="HorizontalOptions" Value="Center" />
                <Setter Property="FontSize" Value="Large" />
                <Setter Property="TextColor" Value="Red" />
            </Style>
        </ResourceDictionary>
     </ContentPage.Resources>
    <ContentPage.Content>
      ...
    </ContentPage.Content>
</ContentPage>

```

Дополнительные сведения о ресурсах приложений см. в статье [`Working with Styles`](~/xamarin-forms/user-interface/styles/index.md).

<a name="rendererpattern" />

## <a name="use-the-custom-renderer-pattern"></a>Использование шаблона настраиваемого отрисовщика

Большинство классов отрисовщиков предоставляют метод `OnElementChanged`, который вызывается при создании пользовательского элемента управления Xamarin.Forms для отрисовки соответствующего собственного элемента управления. Затем классы настраиваемых отрисовщиков (в каждом соответствующем платформе классе отрисовщика) переопределяют этот метод, чтобы создать и настроить собственный элемент управления. Метод `SetNativeControl` используется для построения собственного элемента управления и присваивает свойству `Control` ссылку на элемент управления.

Однако в некоторых случаях метод `OnElementChanged` может вызываться несколько раз. Поэтому в целях предотвращения утечек памяти, которые могут негативно повлиять на производительность, необходимо с осторожностью создавать собственный элемент управления. В следующем примере кода показан подход, используемый при создании собственного элемента управления в пользовательском отрисовщике:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (Control == null) {
    // Instantiate the native control
  }

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    // Configure the control and subscribe to event handlers
  }
}
```

Собственный элемент управления должен создаваться только один раз, когда свойству `Control` задано значение `null`. Элемент управления следует настраивать и подписывать на обработчики событий только при присоединении пользовательского отрисовщика к новому элементу Xamarin.Forms. Аналогично отписываться от обработчиков событий следует только при изменении элемента с подключенным отрисовщиком. Реализовав этот подход, вы сможете создать эффективно работающий настраиваемый отрисовщик, на который не влияют утечки памяти.

Дополнительные сведения о настраиваемых отрисовщиках см. в статье [Настройка элементов управления на каждой платформе](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

## <a name="summary"></a>Сводка

В этой статье были рассмотрены методы повышения производительности приложений Xamarin.Forms. Вместе они могут значительно снизить загрузку ЦП и сократить объем памяти, используемой приложением.


## <a name="related-links"></a>Связанные ссылки

- [Кроссплатформенная производительность](~/cross-platform/deploy-test/memory-perf-best-practices.md)
- [Производительность элемента управления ListView](~/xamarin-forms/user-interface/listview/performance.md)
- [Быстрые отрисовщики](~/xamarin-forms/internals/fast-renderers.md)
- [Сжатие макета](~/xamarin-forms/user-interface/layouts/layout-compression.md)
- [Пример средства изменения размеров изображения Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/XamFormsImageResize/)
- [XamlCompilation](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilation/)
- [XamlCompilationOptions](https://developer.xamarin.com/api/type/Xamarin.Forms.Xaml.XamlCompilationOptions/)
