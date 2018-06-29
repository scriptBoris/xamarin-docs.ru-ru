---
title: 'Xamarin.Essentials: MainThread'
description: Класс MainThread позволяет приложениям выполнять код на основной поток.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E1
author: charlespetzold
ms.author: chape
ms.date: 06/26/2018
ms.openlocfilehash: e07d36d3e9a5492e6e170b62dbacb36be44dbfa9
ms.sourcegitcommit: 72450a6a29599fa133ff4f16fb0b1f443d89f9dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37080546"
---
# <a name="xamarinessentials-mainthread"></a>Xamarin.Essentials: MainThread

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**MainThread** класса позволяет выполнять код в основном потоке выполнения приложения и может ли определенного блока кода выполняется в данный момент в основном потоке.

## <a name="background"></a>Фон

В большинстве операционных систем, включая iOS, Android и универсальная платформа Windows — использовать однопоточную модель для кода, включающих пользовательский интерфейс. Эта модель необходима для правильной сериализации события пользовательского интерфейса, включая нажатия клавиш и сенсорного ввода. Этот поток часто называют _основной поток_ или _потока пользовательского интерфейса_ или _поток пользовательского интерфейса_. Недостаток этой модели является то, что весь код, который обращается к элементы пользовательского интерфейса необходимо запустить для основного потока приложения. 

Иногда, приложения должны использовать события, которые вызывает обработчик событий на вторичный поток выполнения. (Классы Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), и [ `OrientationSensor` ](orientation-sensor.md) все может возвратить сведения для второго потока при использовании с скорость работы.) Если обработчик событий должен получить доступ к элементам пользовательского интерфейса, его необходимо запустить этот код в основном потоке. **MainThread** класс позволяет приложению для выполнения этого кода в основном потоке.

## <a name="running-code-on-the-main-thread"></a>Выполнение кода в основном потоке

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Для выполнения кода в основном потоке Вызовите статический `MainThread.BeginInvokeOnMainThread` метод. Аргумент является [ `Action` ](xref:System.Action) объекта, который является просто метод без аргументов, возвращаемое значение отсутствует:

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

Можно выполнять этот метод в основном потоке, ссылаясь на его в `BeginInvokeOnMainThread` метод:

```csharp
MainThread.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms содержит метод с именем [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) , который выполняет то же самое, что `MainThread.BeginInvokeOnMainThread(Action)`. При использовании любого из методов в приложении Xamarin.Forms рассмотрите, имеет ли вызывающий код любого необходимого для зависимости на Xamarin.Forms. В противном случае `MainThread.BeginInvokeOnMainThread(Action)` , скорее всего, лучшим вариантом.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Определение, если код выполняется в основной поток

`MainThread` Класса также позволяет приложению определять, запущен ли определенного блока кода в основном потоке. `IsMainThread` Возвращает `true` при вызове свойства код выполняется в основном потоке. Это свойство можно использовать программы для запуска другой код для основного потока или второго потока:

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

Может возникнуть вопрос, если следует проверить, если код выполняется во вторичном потоке перед вызовом `BeginInvokeOnMainThread`, например, следующим образом:

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

Следует предположить, что эта проверка может способствовать повышению производительности, если блок кода в основном потоке уже выполняется.

_Тем не менее эта проверка не требуется._ Реализации платформы `BeginInvokeOnMainThread` сами проверки, если вызов выполняется в основном потоке. Производительность снижается, очень мало при вызове метода `BeginInvokeOnMainThread` при не действительно необходимо.

## <a name="api"></a>API

- [MainThread исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/MainThread)
- [Документация по MainThread API](xref:Xamarin.Essentials.MainThread)
