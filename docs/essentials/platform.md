---
title: Xamarin.Essentials платформы
description: Класс платформы позволяет приложениям выполнять код на основной поток.
ms.assetid: CD6D51E7-D933-4FE7-A7F7-392EF27812E0
author: charlespetzold
ms.author: chape
ms.date: 06/03/2018
ms.openlocfilehash: 82e248ee702e104dff98b342aec72179273fc34f
ms.sourcegitcommit: d9ecac62bcf743aff52408fbbd09c5509921a000
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36329951"
---
# <a name="xamarinessentials-platform"></a>Xamarin.Essentials платформы

![Предварительная версия NuGet](~/media/shared/pre-release.png)

**Платформы** класса позволяет выполнять код в основном потоке выполнения приложения и может ли определенного блока кода выполняется в данный момент в основном потоке.

## <a name="background"></a>Фон

В большинстве операционных систем, включая iOS, Android и универсальная платформа Windows — использовать однопоточную модель для кода, включающих пользовательский интерфейс. Эта модель необходима для правильной сериализации события пользовательского интерфейса, включая нажатия клавиш и сенсорного ввода. Этот поток часто называют _основной поток_ или _потока пользовательского интерфейса_ или _поток пользовательского интерфейса_. Недостаток этой модели является то, что весь код, который обращается к элементы пользовательского интерфейса необходимо запустить для основного потока приложения. 

Иногда, приложения должны использовать события, которые вызывает обработчик событий на вторичный поток выполнения. (Классы Xamarin.Essentials [ `Accelerometer` ](accelerometer.md), [ `Compass` ](compass.md), [ `Gyroscope` ](gyroscope.md), [ `Magnetometer` ](magnetometer.md), и [ `OrientationSensor` ](orientation-sensor.md) все может возвратить сведения для второго потока при использовании с скорость работы.) Если обработчик событий должен получить доступ к элементам пользовательского интерфейса, его необходимо запустить этот код в основном потоке. **Платформы** класс позволяет приложению для выполнения этого кода в основном потоке.

## <a name="running-code-on-the-main-thread"></a>Выполнение кода в основном потоке

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Для выполнения кода в основном потоке Вызовите статический `Platform.BeginInvokeOnMainThread` метод. Аргумент является [ `Action` ](xref:System.Action) объекта, который является просто метод без аргументов, возвращаемое значение отсутствует:

```csharp
Platform.BeginInvokeOnMainThread(() =>
{
    // Code to run on the main thread
});
```

Если необходимо вызвать этот метод в приложении Xamarin.Forms в пределах класса, производного от `Element` (включая любой класс, производный от `View` или `Page`), `Platform` имя класса должно полностью соответствовать Имя пространства имен:

```csharp
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(() =>
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
Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
```

> [!NOTE]
> Xamarin.Forms содержит метод с именем [ `Device.BeginInvokeOnMainThread(Action)` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.device.begininvokeonmainthread) , который выполняет то же самое, что `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)`. При использовании любого из методов в приложении Xamarin.Forms рассмотрите, имеет ли вызывающий код любого необходимого для зависимости на Xamarin.Forms. В противном случае `Xamarin.Essentials.Platform.BeginInvokeOnMainThread(Action)` , скорее всего, лучшим вариантом.

## <a name="determining-if-code-is-running-on-the-main-thread"></a>Определение, если код выполняется в основной поток

`Platform` Класса также позволяет приложению определять, запущен ли определенного блока кода в основном потоке. `IsMainThread` Возвращает `true` при вызове свойства код выполняется в основном потоке. Это свойство можно использовать программы для запуска другой код для основного потока или второго потока:

```csharp
if (Xamarin.Essentials.Platform.IsMainThread)
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
if (Xamarin.Essentials.Platform.IsMainThread)
{
    MyMainThreadCode();
}
else
{
    Xamarin.Essentials.Platform.BeginInvokeOnMainThread(MyMainThreadCode);
}
```

Следует предположить, что эта проверка может способствовать повышению производительности, если блок кода в основном потоке уже выполняется.

_Тем не менее эта проверка не требуется._ Реализации платформы `BeginInvokeOnMainThread` сами проверки, если вызов выполняется в основном потоке. Производительность снижается, очень мало при вызове метода `BeginInvokeOnMainThread` при не действительно необходимо.

## <a name="api"></a>API

- [Исходный код платформы](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Platform)
- [Платформа API документация](xref:Xamarin.Essentials.Platform)
