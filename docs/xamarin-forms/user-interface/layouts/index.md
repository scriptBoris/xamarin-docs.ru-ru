---
title: Макеты в Xamarin.Forms
description: Xamarin.Forms имеет различные макеты и функции для организации содержимого на экране, и в этой статье объясняется их.
ms.prod: xamarin
ms.assetid: 65030DA3-C7C1-4A02-B478-811073C39139
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: df2bd07ad5d91c237320b74d7a81828ba1c062c2
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053277"
---
# <a name="layouts-in-xamarinforms"></a>Макеты в Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)

Xamarin.Forms есть различные макеты и функции для организации содержимого на экране.

> [!VIDEO https://youtube.com/embed/4HlLjTZQzjM]

**Макеты Xamarin.Forms, по [Xamarin University](https://university.xamarin.com/)**

Каждый элемент управления макета описан ниже, а также сведения о том, как обрабатывать изменения ориентации экрана.

* **[StackLayout](stack-layout.md)**  &ndash; используется для размещения представления линейно, горизонтально или вертикально. Представления в StackLayout могут быть выровнены по центру, влево или вправо макета.
* **[AbsoluteLayout](absolute-layout.md)**  &ndash; позволяет упорядочить представления, задав координаты, ра & змер с точки зрения абсолютные значения или соотношений. AbsoluteLayout может использоваться для уровня представления, а также привязывать их к слева, справа или center.
* **[RelativeLayout](relative-layout.md)**  &ndash; используется для размещения представления, задав ограничения относительно их родительского элемента размеры и положение.
* **[Сетка](grid.md)**  &ndash; используется для размещения представления в виде сетки. С точки зрения абсолютные значения или соотношений, можно указать строки и столбцы.
* **[FlexLayout](flex-layout.md)**  &ndash; используется для размещения представления по горизонтали или по вертикали при переносе.
* **[ScrollView](scroll-view.md)**  &ndash; используется для предоставления прокрутку, если представление не может поместиться полностью в пределах границ экрана.
* **[LayoutOptions](layout-options.md)**  &ndash; определяют выравнивание и расширения для представления, относительно его родительского элемента.
* **[Входные данные прозрачности](#input_transparency)**  &ndash; указывает, является ли элемент получает входные данные.
* **[Поля и заполнение](margin-and-padding.md)**  &ndash; показано, как управлять поведением макета при подготовке к просмотру элемента в пользовательском интерфейсе.
* **[Ориентация устройства](device-orientation.md)**  &ndash; объясняет, как обрабатывать изменения ориентации устройства.
* **[Макет на устройства планшетных и настольных компьютеров](tablet.md)**  &ndash; показано, как оптимизация для больших экранов на каждой платформе.
* **[Создание пользовательского макета](custom.md)**  &ndash; объясняет, как создать класс пользовательского макета.
* **[Сжатие макета](layout-compression.md)**  &ndash; Удаляет указанный макет из визуального дерева с целью повышения скорости отрисовки страниц.

Элементы управления платформы также может использоваться непосредственно в макеты Xamarin.Forms с [ **собственного внедрение** ](~/xamarin-forms/platform/native-views/index.md) (новое в Xamarin.Forms 2.2), и вы можете [ **создавать собственные макеты** ](custom.md) в соответствии с потребностями.

Следующий рисунок отображает элементы управления макета:

[![](images/layouts-sml.png "Макеты Xamarin.Forms")](images/layouts.png#lightbox "макеты Xamarin.Forms")

## <a name="choosing-the-right-layout"></a>Выбор макета вправо

Макеты, выбранная в приложении можно помочь или повредит вам при создании приложения Xamarin.Forms привлекательным и может использоваться. Занимает некоторое время, которые следует учитывать, как работает каждый макет может помочь написать более понятным и более масштабируемым код пользовательского интерфейса. Экран можно сочетать различные макеты для достижения конкретного варианта.

### <a name="stacklayoutstack-layoutmd"></a>[StackLayout](stack-layout.md)

`StackLayout` Используется для отображения представления вдоль горизонтальной или вертикальной линии. Положение и размер в макете определяется на основе представления `HeightRequest`, `WidthRequest`, `HorizontalOptions` и `VerticalOptions`. `StackLayout` часто используется как базовый макет, упорядочение других значений, на экране.

Например, когда `StackLayout` следует быть хорошим выбором, рассмотрим приложение, которое нужно отобразить кнопку и метку, с выравниванием по левому краю метки и кнопку по правому краю.

```xaml
<StackLayout Orientation="Horizontal">
  <Label HorizontalOptions="StartAndExpand" Text="Label" />
  <Button HorizontalOptions="End" Text="Button" />
</StackLayout>
```

### <a name="flexlayoutflex-layoutmd"></a>[FlexLayout](flex-layout.md)

`FlexLayout` Аналогичен `StackLayout` тем, что показывает дочерних представлений по горизонтали или по вертикали:

```xaml
<FlexLayout Direction="Column"
            AlignItems="Center"
            JustifyContent="SpaceEvenly">

    <Label Text="FlexLayout in Action" />
    <Button Text="Button" />
    <Label Text="Another Label" />
</FlexLayout>
```

Тем не менее, если слишком много дочерних элементов, чтобы уместиться в одной строке или столбцу, `FlexLayout` поддерживает упаковки эти представления. `FlexLayout` на основании CSS гибкий модуль макета и имеется много тех же встроенных параметров для размещения и выравнивания его дочерних элементов.

### <a name="absolutelayoutabsolute-layoutmd"></a>[AbsoluteLayout](absolute-layout.md)

`AbsoluteLayout` Используется для отображения представлений, а также размер и положение указывается либо как явные значения или значения относительно размера макета. В отличие от `StackLayout` и `Grid`, `AbsoluteLayout` позволяет дочерние представления, к их наложению. В отличие от `RelativeLayout`, `AbsoluteLayout` не позволяет размещать элементы вне экрана.

Например, когда `AbsoluteLayout` следует быть хорошим выбором, рассмотрим приложение, которое требуется для представления коллекции объектов в виде стеки. Это часто происходит в том при представлении альбомы фотографий или песни. Следующий код предоставляет внешний вид стопке, с элементами, повернуто некоторые содержимое накопление:

В XAML:

```xaml
<AbsoluteLayout Padding="15">
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="30"
    Source="bottom.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100" Rotation="60"
    Source="middle.png" />
  <Image AbsoluteLayout.LayoutFlags="PositionProportional" AbsoluteLayout.LayoutBounds="0.5, 0, 100, 100"
    Source="cover.png" />
</AbsoluteLayout>
```

Обратите внимание, приведенный выше код следующие аспекты:

- Каждый `Image` отображается в той же позиции (в середине пространство по горизонтали)
- `Padding` Считается `AbsoluteLayout`, в отличие от `RelativeLayout`, которая автоматически его игнорируют.
- `AbsoluteLayout.LayoutFlags` Указывает, каким образом будут интерпретироваться границы макета. В этом случае `PositionProportional`, означает, что координаты доли размер макета, хотя размер будет интерпретироваться как размер, явно.
- `AbsoluteLayout.Layoutbounds` задает горизонтальное положение, вертикальное положение, ширину и высоту в указанном порядке.

### <a name="relativelayoutrelative-layoutmd"></a>[RelativeLayout](relative-layout.md)

`RelativeLayout` Используется для отображения представления, с учетом размера и позиции, указанной в качестве значений по отношению к значениям макета или другое представление. Относительные значения не обязательно должны соответствовать ему соответствующее значение в связанное представление. Например, можно задать представление `Width` свойство было пропорционально в другое представление `X` свойство.

RelativeLayout можно использовать для создания пользовательских интерфейсов, которые масштабируются пропорционально путем размеры устройств. Следующий XAML реализует макета с полями в верхних углах flagpole с флагом в центре:

```xaml
<RelativeLayout HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand">
  <BoxView Color="Blue" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = 0}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Red" HeightRequest="50" WidthRequest="50"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .9}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = 0}" />
  <BoxView Color="Gray" WidthRequest="15" x:Name="pole"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.75}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToParent, Property=Width, Factor = .45}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor = .25}" />
  <BoxView Color="Green"
    RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=.10, Constant=10}"
    RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.2, Constant=20}"
    RelativeLayout.XConstraint= "{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=X, Constant=15}"
    RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=pole, Property=Y, Constant=0}" />
</RelativeLayout>
```

Обратите внимание, приведенный выше код следующие аспекты:

- Положения и размеры указываются в качестве ограничений.
- Flagpole имеет имя, чтобы флаг (зеленое поле) можно задать положение относительно flagpole.
- Выражения ограничения имеют `Factor` и `Constant` свойства, которые можно использовать для определения положения и размеры, кратные (или дроби) свойств других объектов, а также константа. Константы могут быть отрицательными.

### <a name="gridgridmd"></a>[Сетка](grid.md)

`Grid` Используется для отображения элементов строк и столбцов. Обратите внимание на то, что сетка не является таблицей, поэтому он не поддерживает концепцию ячеек, строк верхний и нижний колонтитулы или границы между строками и столбцами. В общем случае сетка не подходит для отображения табличных данных. Для подобного использования, рассмотрите возможность [ListView](~/xamarin-forms/user-interface/listview/index.md) или [TableView](~/xamarin-forms/user-interface/tableview.md).

Например, когда `Grid` — справа макет для использования, рассмотрите возможность числового ввода для калькулятора. Числовой ввод для калькулятора может состоять из четырех строк и три столбца, каждый из которых кнопки. Следующий код реализует такой подход:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>

  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Button Text="1" Grid.Row="0" Grid.Column="0" />
  <Button Text="2" Grid.Row="0" Grid.Column="1" />
  <Button Text="3" Grid.Row="0" Grid.Column="2" />
  <Button Text="4" Grid.Row="1" Grid.Column="0" />
  <Button Text="5" Grid.Row="1" Grid.Column="1" />
  <Button Text="6" Grid.Row="1" Grid.Column="2" />
  <Button Text="7" Grid.Row="2" Grid.Column="0" />
  <Button Text="8" Grid.Row="2" Grid.Column="1" />
  <Button Text="9" Grid.Row="2" Grid.Column="2" />
  <Button Text="0" Grid.Row="3" Grid.Column="1" />
  <Button Text="&lt;-" Grid.Row="3" Grid.Column="2" />
</Grid>
```

Обратите внимание, приведенный выше код следующие аспекты:

- Таблицы и столбцы указаны явно, не выведены из содержимого.
- `Height` и `Width` значения могут быть установлены в star, это означает, что сетка установит эти значения для заполнения доступного пространства.
- Положение каждой кнопки определяется с `Grid.Row`  &  `Grid.Column` свойства.

### <a name="layoutoptionslayout-optionsmd"></a>[LayoutOptions](layout-options.md)

[ `LayoutOptions` ](xref:Xamarin.Forms.LayoutOptions) Структуры можно использовать для определения, выравнивание и расширения для представления, относительно его родительского элемента.

### <a name="margin-and-paddingmargin-and-paddingmd"></a>[Поля и заполнение](margin-and-padding.md)

[ `Margin` ](xref:Xamarin.Forms.View.Margin) И [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) свойства управляют поведением макета, когда элемент отображается в пользовательском интерфейсе.

<a name="input_transparency" />

### <a name="input-transparency"></a>Входной прозрачности

Каждый элемент имеет [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) свойство, которое используется для определения, является ли элемент получает входные данные. Значение по умолчанию — `false`, гарантируя, что элемент получает входные данные.

Если это свойство имеет значение на классе контейнера, например класс макета, а его значение передается дочерних элементов. Таким образом, задание [ `InputTransparent` ](xref:Xamarin.Forms.VisualElement.InputTransparent) свойства `true` в макете приведет к класса элементов макета, не получает входные данные.

### <a name="device-orientationdevice-orientationmd"></a>[Ориентация устройства](device-orientation.md)

Xamarin.Forms и его встроенные макеты способны обработка изменений в ориентации устройства. Рассмотрим, какие ориентации, будет поддерживаться в вашем приложении, а также как вы сделаете использовать поле в альбомной и портретной режимах.

### <a name="layout-for-tablet-and-desktop-appstabletmd"></a>[Макет для планшета и классических приложений](tablet.md)

iOS, Android и универсальной платформы Windows все поддерживать большие размеры экрана на планшетных устройствах (а также настольных и переносных компьютеров для Windows). Xamarin.Forms позволяет оптимизировать приложения для больших экранов, определение типа устройства и либо подгонки макета страницы или полностью используя совсем другую страницу для больших экранов.

### <a name="creating-a-custom-layoutcustommd"></a>[Создание пользовательского макета](custom.md)

Xamarin.Forms определяет четыре класса макета - [ `StackLayout` ](xref:Xamarin.Forms.StackLayout), [ `AbsoluteLayout` ](xref:Xamarin.Forms.AbsoluteLayout), [ `RelativeLayout` ](xref:Xamarin.Forms.RelativeLayout), и [ `Grid` ](xref:Xamarin.Forms.Grid), и каждый упорядочивает его дочерние элементы по-разному. Тем не менее, иногда внес необходимые для организации содержимого страницы, с помощью макета, не предоставляемые Xamarin.Forms. В этой статье объясняется, как создать класс пользовательского макета и демонстрирует учетом ориентации `WrapLayout` класс, который упорядочивает его дочерние элементы горизонтально по странице и затем создает оболочку для отображения последующих дочерних элементов для дополнительных строк.

### <a name="layout-compressionlayout-compressionmd"></a>[Сжатие макета](layout-compression.md)

Сжатие макета позволяет удалить указанные макеты из визуального дерева с целью повышения скорости отрисовки страниц. В этом случае рост производительности зависит от сложности страницы, версии используемой операционной системы и устройства, на котором выполняется приложение. Однако наиболее заметное повышение производительности будет наблюдаться на старых устройствах.

## <a name="making-your-choice"></a>Ваш выбор

Имейте в виду, что в большинстве случаев несколько вариантов макета может использоваться для реализации нужного проекта. При наличии нескольких допустимых значений, необходимо определить, какой подход будут самым простым для вашей ситуации.
Большинство схем невозможно реализовать только один макет, поэтому макеты вложенности, как требуется для создания более сложных конструкций.


## <a name="related-links"></a>Связанные ссылки

- [Рекомендации по работе с человеческим интерфейсом Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG)
- [Веб-сайт конструктора Android](https://developer.android.com/design/index.html)
- [Макет (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Пример BusinessTumble (пример)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
