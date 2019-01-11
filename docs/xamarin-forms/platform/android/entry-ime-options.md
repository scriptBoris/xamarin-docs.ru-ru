---
title: Параметры редактора метода ввода для записи на устройстве Android
description: Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, без реализации пользовательских модулей подготовки отчетов или эффектов. В этой статье объясняется, как использовать Android конкретных платформ, задает параметры редактора для программируемой клавиатуры для операции данный метод ввода.
ms.prod: xamarin
ms.assetid: 7909C738-04B2-4476-9A3B-A6D79BC3B9B2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 930614b2479d0edcdba74ed3f98a169491252c63
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54208919"
---
# <a name="entry-input-method-editor-options-on-android"></a>Параметры редактора метода ввода для записи на устройстве Android

[![Скачать пример](~/media/shared/download.png) Скачать пример](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Это Android специфические для платформы задает метод ввода параметров редактора (IME) для программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry). Это включает в себя задание кнопки действие пользователя в нижнем углу программируемой клавиатуры и взаимодействия с `Entry`. Он используется в XAML, задав [ `Entry.ImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.ImeOptionsProperty) присоединенное свойство в значение [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) перечисления:

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout ...>
        <Entry ... android:Entry.ImeOptions="Send" />
        ...
    </StackLayout>
</ContentPage>
```

Кроме того его можно будет использовать с помощью C# с помощью текучего API:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

entry.On<Android>().SetImeOptions(ImeFlags.Send);
```

`Entry.On<Android>` Метод указывает, что этой платформы будет выполняться только в Android. [ `Entry.SetImeOptions` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Entry.SetImeOptions(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Entry},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags)) Метод в [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) пространства имен, используется для задания параметра действия метода ввода для программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry), с помощью [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) перечисления, предоставляя следующие значения:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Default) — Указывает, что ключ не определенное действие является обязательным, и что базового элемента управления будет создавать самостоятельно при его можно. Это будет `Next` или `Done`.
- [`None`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.None) — Указывает, что ключ не действия будут доступны.
- [`Go`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Go) — Указывает, что ключ действия будет выполнять операции «go», когда пользователь в целевой объект текста, было введено.
- [`Search`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Search) — Указывает, что ключ действия выполняет операцию «поиск», когда пользователь в результаты поиска в тексте будет их ввода.
- [`Send`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Send) — Указывает, что ключ действия выполнит операцию «отправить», предоставляя текста своей цели.
- [`Next`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Next) — Указывает, что ключ действия выполнит операцию «Далее», когда пользователь будет к следующему полю, которая будет принимать текст.
- [`Done`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Done) — Указывает, что ключ действия выполнит операцию «Готово», закрытие экранной клавиатуры.
- [`Previous`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.Previous) — Указывает, что ключ действия выполнит операцию «раньше», когда пользователь будет к предыдущему полю, которая будет принимать текст.
- [`ImeMaskAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.ImeMaskAction) — маску для выбора параметров действия.
- [`NoPersonalizedLearning`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoPersonalizedLearning) — Указывает, что средство проверки орфографии будет ни Узнайте от пользователя, а также их решения в зависимости от того, что ранее введенное пользователем.
- [`NoFullscreen`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoFullscreen) — Указывает, что пользовательский Интерфейс не должен переходить в полноэкранном режиме.
- [`NoExtractUi`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoExtractUi) — Указывает, что пользовательский Интерфейс не отображается для извлеченного текста.
- [`NoAccessoryAction`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags.NoAccessoryAction) — Указывает, что пользовательский Интерфейс не будет отображаться для выполнения пользовательских действий.

Результатом является то, что указанный [ `ImeFlags` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ImeFlags) значение применяется к программируемой клавиатуры для [ `Entry` ](xref:Xamarin.Forms.Entry), присваивающего параметры редактора метода ввода:

[![Запись входных данных метод редактор платформы](entry-ime-options-images/entry-imeoptions.png "платформы редактора метода ввода запись")](entry-ime-options-images/entry-imeoptions-large.png#lightbox "платформы редактора метода ввода запись")

## <a name="related-links"></a>Связанные ссылки

- [PlatformSpecifics (пример)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Создание особенностей платформы](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
