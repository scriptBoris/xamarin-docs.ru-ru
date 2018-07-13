---
title: Класс устройств Xamarin.Forms
description: В этой статье объясняется, как использовать класс устройств Xamarin.Forms, возможность точного управления функциональные возможности и макеты на каждой платформы.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c706d50962fb707208203a97374d4ae26f141ebf
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998271"
---
# <a name="xamarinforms-device-class"></a>Класс устройств Xamarin.Forms

[ `Device` ](xref:Xamarin.Forms.Device) Класс содержит несколько свойств и методов, чтобы помочь разработчикам настраивать макет и функций на каждой платформы.

В дополнение к методам и свойствам для целевого кода по отдельным типам оборудования и размеры `Device` класс включает [BeginInvokeOnMainThread](#Device_BeginInvokeOnMainThread) метод, который должен использоваться при взаимодействии с помощью пользовательского интерфейса элементов управления из фоновые потоки.

<a name="providing-platform-values" />

## <a name="providing-platform-specific-values"></a>Предоставление значений платформы

До Xamarin.Forms 2.3.4, платформы, приложение было запущено на удалось получить, изучив [ `Device.OS` ](xref:Xamarin.Forms.Device.OS) свойство и его для сравнения [ `TargetPlatform.iOS` ](xref:Xamarin.Forms.TargetPlatform.iOS), [ `TargetPlatform.Android` ](xref:Xamarin.Forms.TargetPlatform.Android), [ `TargetPlatform.WinPhone` ](xref:Xamarin.Forms.TargetPlatform.WinPhone), и [ `TargetPlatform.Windows` ](xref:Xamarin.Forms.TargetPlatform.Windows) значений перечисления. Аналогично, один из [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) перегрузки может использоваться для предоставления значений, зависящих от платформы к элементу управления.

Тем не менее поскольку Xamarin.Forms 2.3.4 эти API устарели и заменены. [ `Device` ](xref:Xamarin.Forms.Device) Класс теперь содержит общедоступный строковые константы, определяющие платформ — [ `Device.iOS` ](xref:Xamarin.Forms.Device.iOS), [ `Device.Android` ](xref:Xamarin.Forms.Device.Android), `Device.WinPhone`() Устаревшая версия), `Device.WinRT` (устаревшая версия), [ `Device.UWP` ](xref:Xamarin.Forms.Device.UWP), и [ `Device.macOS` ](xref:Xamarin.Forms.Device.macOS). Аналогичным образом [ `Device.OnPlatform` ](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) перегрузки были заменены [ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) и [ `On` ](xref:Xamarin.Forms.On) API-интерфейсы.

В C# с платформой значения можно задать путем создания `switch` оператором в [ `Device.RuntimePlatform` ](xref:Xamarin.Forms.Device.RuntimePlatform) свойства, а затем предоставить `case` инструкций для необходимых платформ:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) И [ `On` ](xref:Xamarin.Forms.On) классы предоставляют те же функциональные возможности, в XAML:

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

[ `OnPlatform` ](xref:Xamarin.Forms.OnPlatform`1) Класс — это универсальный класс и поэтому должен быть создан при помощи `x:TypeArguments` атрибут, который соответствует целевому типу. В [ `On` ](xref:Xamarin.Forms.On) класс, [ `Platform` ](xref:Xamarin.Forms.On.Platform) атрибут может принимать один `string` значение или несколько разделенных запятыми `string` значения.

> [!IMPORTANT]
> Предоставляя некорректное `Platform` значение в атрибута `On` не приведут к ошибке. Вместо этого код будет выполняться без значения платформы, которые применяются.

<a name="Device_Idiom" />

## <a name="deviceidiom"></a>Device.Idiom

`Device.Idiom` Можно использовать для изменения макеты или функциональные возможности в зависимости от приложения на устройства. [ `TargetIdiom` ](xref:Xamarin.Forms.TargetIdiom) Перечисление содержит следующие значения:

-  **Телефон** — iPhone, iPod touch и уже, чем 600 частные интерфейсы Android ^
-  **Tablet** — iPad, Windows и Android устройств шире, чем 600 спады ^
-  **Рабочий стол** — возвращаются только в [приложений универсальной платформы Windows](~/xamarin-forms/platform/windows/installation/index.md) на настольных компьютерах Windows 10 (возвращает `Phone` на мобильных устройствах Windows, в том числе в сценариях непрерывное множество решений)
-  **TV** — Tizen TV устройств
-  **Контрольные значения** — Tizen watch устройств
-  **Неподдерживаемый** – не используется

*^ частные интерфейсы не обязательно является количество физических пикселей*

`Idiom` особенно полезна для создания макетов, которые используют преимущества экраны больше, следующим образом:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

## <a name="deviceflowdirection"></a>Device.FlowDirection

[ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) Значение извлекает [ `FlowDirection` ](xref:Xamarin.Forms.FlowDirection) значение перечисления, представляющее текущее направление потока, который используется устройством. Направление потока является направление, в котором проверяются элементы пользовательского интерфейса на странице глаза. Перечисление имеет следующие значения.

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToRight`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

В XAML [ `Device.FlowDirection` ](xref:Xamarin.Forms.VisualElement.FlowDirection) значение можно получить с помощью `x:Static` расширение разметки:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

Ниже приведен аналогичный код на языке C#:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Дополнительные сведения о направлении, см. в разделе [справа налево локализации](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

<a name="Device_Styles" />

## <a name="devicestyles"></a>Device.Styles

[ `Styles` Свойство](~/xamarin-forms/user-interface/styles/index.md) содержит определения встроенных стилей, которые могут применяться для некоторых элементов управления (такие как `Label`) `Style` свойство. Ниже приведены доступные стили.

* BodyStyle
* CaptionStyle
* ListItemDetailTextStyle
* ListItemTextStyle
* SubtitleStyle
* TitleStyle

<a name="Device_GetNamedSize" />

## <a name="devicegetnamedsize"></a>Device.GetNamedSize

`GetNamedSize` можно использовать при задании [ `FontSize` ](~/xamarin-forms/user-interface/text/fonts.md) в коде C#:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

<a name="Device_OpenUri" />

## <a name="deviceopenuri"></a>Device.OpenUri

`OpenUri` Метод может использоваться для запуска операций на базовой платформы, такие как открытие URL-адрес собственного веб-браузера (**Safari** в iOS или **Internet** в Android).

```csharp
Device.OpenUri(new Uri("https://evolve.xamarin.com/"));
```

[Пример WebView](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithWebview/WorkingWithWebview/WebAppPage.cs) включает пример использования `OpenUri` открывать URL-адреса и сработать телефонные звонки.

[Пример Maps](https://github.com/xamarin/xamarin-forms-samples/blob/master/WorkingWithMaps/WorkingWithMaps/MapAppPage.cs) также использует `Device.OpenUri` для отображения карт и направлений, используя собственные **сопоставляет** приложения на iOS и Android.

<a name="Device_StartTimer" />

## <a name="devicestarttimer"></a>Device.StartTimer

`Device` Класс также имеет `StartTimer` метод, который предоставляет простой способ запустить задачи, зависящие от времени, работающая в общем коде Xamarin.Forms, включая библиотеку .NET Standard. Передайте `TimeSpan` для установки интервала и возврата `true` следует таймер или `false` для ее остановки после текущего вызова.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () => {
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Если код внутри таймер взаимодействует с помощью пользовательского интерфейса (такие как установка текст `Label` или отображая оповещение) должно выполняться внутри `BeginInvokeOnMainThread` выражение (см. ниже).

<a name="Device_BeginInvokeOnMainThread" />

## <a name="devicebegininvokeonmainthread"></a>Device.BeginInvokeOnMainThread

Элементы пользовательского интерфейса не должны быть доступны фоновые потоки, например код, выполняемый в таймер или обработчик завершения асинхронной операции, такие как веб-запросов. Любой код фона, который необходимо обновить пользовательский интерфейс, которые должны быть вставлены в [ `BeginInvokeOnMainThread` ](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)). Это является эквивалентом `InvokeOnMainThread` на iOS, `RunOnUiThread` в Android и `Dispatcher.RunAsync` на универсальной платформе Windows.

Код Xamarin.Forms является:

```csharp
Device.BeginInvokeOnMainThread ( () => {
  // interact with UI elements
});
```

Обратите внимание что методы, с помощью `async/await` не обязательно должны использовать `BeginInvokeOnMainThread` ли он из основного потока пользовательского интерфейса.

## <a name="summary"></a>Сводка

Xamarin.Forms `Device` класс обеспечивает точный контроль функциональные возможности и макеты на каждой платформы — даже общего кода (проектов библиотек .NET Standard или общие проекты).


## <a name="related-links"></a>Связанные ссылки

- [Пример устройства](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithDevice/)
- [Образец стилей](https://developer.xamarin.com/samples/xamarin-forms/WorkingWithStyles/)
- [Устройства](xref:Xamarin.Forms.Device)
