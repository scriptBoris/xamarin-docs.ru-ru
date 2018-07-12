---
title: 'Xamarin.Essentials: MainThread'
description: Класс MainThread позволяет выполнять код в основной поток выполнения приложения.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: charlespetzold
ms.author: chape
ms.date: 06/26/2018
ms.openlocfilehash: e07d36d3e9a5492e6e170b62dbacb36be44dbfa9
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831429"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**MainThread** класс позволяет выполнять код в основном потоке выполнения приложения, а также для определения конкретного блока кода выполняется в данный момент в основном потоке.

## <a name="background"></a>Фон

Большинство операционных систем, включая iOS, Android и универсальной платформы Windows — использовать однопоточную модель для кода, включающих пользовательский интерфейс. Эта модель необходима для правильной сериализации событий пользовательского интерфейса, включая нажатия клавиш и сенсорного ввода. Этот поток часто называется _основной поток_ или _UI-потоке_ или _поток пользовательского интерфейса_. Недостаток этой модели является то, что весь код, который обращается к элементы пользовательского интерфейса необходимо запустить для основного потока приложения. 

Иногда приложения должны использовать события, которые вызывают обработчик событий во вторичном потоке выполнения. (Классы Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), и [ `OrientationSensor` ](orientation-sensor.md) все могут возвращать сведения во вторичном потоке при использовании с более высокой скоростью.) Если обработчик событий должен получить доступ к элементам пользовательского интерфейса, его необходимо запустить этот код в основном потоке. **MainThread** класс позволяет приложению для выполнения этого кода в основном потоке.

## <a name="running-code-on-the-main-thread"></a>Выполнение кода в основном потоке

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Чтобы запустить код в основном потоке, вызовите статический `MainThread.BeginInvokeOnMainThread` метод. Аргумент является [ `Action` ](xref:System.Action) объект, который является просто метод с без аргументов и возвращаемых значений:

```csharp
MainThread.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Можно также определить отдельный метод для кода, который должен выполняться в основном потоке:

```csharp
void MyMainThreadCode()
{
    // Code to run on the main thread
}
```

Затем можно запустить этот метод в основном потоке, ссылающихся на него в `BeginInvokeOnMainThread` метод:

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms имеет метод под названием [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) , делает то же самое `MainThread.BeginInvokeOnMainThread(Action)`. При использовании любого из этих методов в приложении Xamarin.Forms, рассмотрите возможность ли вызывающий код имеет любой другой потребность в зависимости от Xamarin.Forms. В противном случае `MainThread.BeginInvokeOnMainThread(Action)` скорее всего является лучшим вариантом.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Определение, если код выполняется на основной поток

`MainThread` Класс также позволяет приложению определить определенного блока кода выполняется в основном потоке. `IsMainThread` Возвращает `true` Если код, вызвав свойство выполняется в основном потоке. Это свойство можно использовать программу для запуска другой код для основного потока или вторичном потоке:

```csharp
if (MainThread.IsMainThread)
{
    // Code to run if this is the main thread
}
else
{
    // Code to run if this is a secondary thread
}
```

Может возникнуть вопрос, если следует проверить, работает ли код во вторичном потоке перед вызовом `BeginInvokeOnMainThread`, например, следующим образом:

```csharp
if (MainThread.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Следует ожидать, что эта проверка может повысить производительность, если блок кода уже выполняется в основном потоке.

_Тем не менее эта проверка не требуется._ Реализации платформы `BeginInvokeOnMainThread` сами проверить, если вызов выполняется в основном потоке. Производительность снижается, очень мало при вызове метода `BeginInvokeOnMainThread` когда не действительно необходимо.

## <a name="api"></a>API

- [MainThread исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Документация по MainThread API](xref:Xamarin.Essentials.MainThread)
