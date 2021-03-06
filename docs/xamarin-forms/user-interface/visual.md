---
title: Xamarin.Forms Visual
description: В этой статье рассматриваются Xamarin.Forms визуальный элемент, который отображает представления одинаково, или во многом идентично в iOS и Android.
ms.prod: xamarin
ms.assetid: 80BF9C72-AC28-4AAF-9DDD-B60CBDD1CD59
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/12/2018
ms.openlocfilehash: df2351e35817a6468fed236752eea8e5ad11024f
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53061890"
---
# <a name="xamarinforms-visual"></a>Xamarin.Forms Visual

![Предварительный просмотр](~/media/shared/preview.png)

_В этой статье рассматриваются Xamarin.Forms визуальный элемент, который отображает представления одинаково, или во многом идентично в iOS и Android._

Многие разработчики желают создавать приложений Xamarin.Forms, которые выглядят идентичными или мало чем отличается в iOS и Android. Xamarin.Forms 4.0-pre1 включает механизм для включения дополнительных модулей подготовки отчетов, которые реализуют внешний вид, с приложениями, переключиться на внешний через `Visual` свойство:

```xaml
<ContentPage ...
             Visual="Material">
    ...
</ContentPage>    
```

Модули подготовки отчетов, которые реализуют внешний вид затем используются для визуализации представления, а не модулями подготовки отчетов по умолчанию. Во время выбора модуля подготовки отчетов `Visual` свойства представления проверен и включены в процесс выбора модуля подготовки отчетов. Кроме того Если `Visual` свойство изменяется во время выполнения, указанного модуля подготовки отчетов создается заново, а также все дочерние элементы.

> [!IMPORTANT]
> `Visual` Свойство определено в `VisualElement` класс с представлениями наследование `Visual` значение свойства от их родительских объектов. Таким образом, задание `Visual` свойство `ContentPage` гарантирует, что все поддерживаемые представления на странице будет использовать этот внешний вид. Кроме того `Visual` свойство можно переопределить для представления.

Xamarin.Forms 4.0-pre1 включает экспериментальный внешний вид, основанные на материального исполнения, со средствами визуализации, как материала модулей подготовки отчетов. Материала модулей подготовки отчетов в настоящее время включены для следующих представлений на iOS и Android:

- [`Button`](xref:Xamarin.Forms.Button)
- [`Entry`](xref:Xamarin.Forms.Entry)
- [`Frame`](xref:Xamarin.Forms.Frame)
- [`ProgressBar`](xref:Xamarin.Forms.ProgressBar)

С функциональной точки зрения материала визуализаторов ничем не отличаются, для модулей подготовки отчетов по умолчанию. Тем не менее, они настоящее время являются экспериментальными и могут использоваться только, добавив следующую строку кода, чтобы ваши `AppDelegate` класс на iOS или для вашей `MainActivity` класс на устройствах Android, перед вызовом метода `Forms.Init`:

```csharp
Forms.SetFlags("Visual_Experimental");
```

Кроме того, в iOS проект платформы должен иметь [Xamarin.iOS.MaterialComponents](https://www.nuget.org/packages/Xamarin.iOS.MaterialComponents/) установленный пакет NuGet. В Android Visual работает только с API 29, платформа проекта необходимо использовать v28 библиотек поддержки и задать его тему наследовать темы компоненты материала или по-прежнему наследовать от темы AppCompat при добавлении некоторые новые атрибуты темы к теме. Дополнительные сведения см. в разделе [Приступая к работе с компонентами материал для Android](https://github.com/material-components/material-components-android/blob/master/docs/getting-started.md).

На следующих снимках экрана показано пользовательский интерфейс, который включает в себя четыре представления, какие материалы существует модулей подготовки отчетов, но отображаются с использованием модулей подготовки отчетов по умолчанию:

[![По умолчанию модули подготовки отчетов](visual-images/default-renderers.png "представлениями с использованием модулей подготовки отчетов по умолчанию")](visual-images/default-renderers-large.png#lightbox)

На следующих снимках экрана показано тот же пользовательский интерфейс, с помощью материалов модулей подготовки отчетов к просмотру:

[![Материала модулей подготовки отчетов](visual-images/material-renderers.png "представлениями с использованием материала модулей подготовки отчетов")](visual-images/material-renderers-large.png#lightbox)

> [!NOTE]
> Готовый для просмотра элементы управления остаются собственные элементы управления материала модулей подготовки отчетов и таким образом будет по-прежнему существовать отличия между платформами сферах, таких как шрифты, теней, цвета и повышение прав в пользовательском интерфейсе.

## <a name="related-links"></a>Связанные ссылки

- [Пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
