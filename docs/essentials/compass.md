---
title: 'Xamarin.Essentials: Compass'
description: В этом документе описывается класс Compass в Xamarin.Essentials, который позволяет отслеживать направление устройства на северный магнитный полюс.
ms.assetid: BF85B0C3-C686-43D9-811A-07DCAF8CDD86
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 55dd10bff21b7d082b225277d0100232d5efd4f3
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898788"
---
# <a name="xamarinessentials-compass"></a>Xamarin.Essentials: Compass

Класс **Compass** позволяет отслеживать направление устройства на северный магнитный полюс.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-compass"></a>Использование класса Compass

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Функции Compass выполняются при вызове методов `Start` и `Stop` для ожидания передачи данных об изменениях данных компаса. Все изменения возвращаются через событие `ReadingChanged`. Пример:

```csharp
public class CompassTest
{
    // Set speed delay for monitoring changes.
    SensorSpeed speed = SensorSpeed.UI;

    public CompassTest()
    {
        // Register for reading changes, be sure to unsubscribe when finished
        Compass.ReadingChanged += Compass_ReadingChanged;
    }

    void Compass_ReadingChanged(object sender, CompassChangedEventArgs e)
    {
        var data = e.Reading;
        Console.WriteLine($"Reading: {data.HeadingMagneticNorth} degrees");
        // Process Heading Magnetic North
    }

    public void ToggleCompass()
    {
        try
        {
            if (Compass.IsMonitoring)
              Compass.Stop();
            else
              Compass.Start(speed);
        }
        catch (FeatureNotSupportedException fnsEx)
        {
            // Feature not supported on device
        }
        catch (Exception ex)
        {
            // Some other exception has occurred
        }
    }
}
```

[!include[](~/essentials/includes/sensor-speed.md)]

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Android не предоставляет API для извлечения данных о направлении компаса. Для вычисления направления на северный магнитный полюс используются акселерометр и магнитометр. Именно такой поход рекомендует Google.

В редких случаях вы можете увидеть непоследовательные результаты, так как датчики нужно калибровать, что предполагает перемещение устройства "по восьмерке". Лучше всего открыть Карты Google, коснуться точки своего расположения и выбрать **калибровку компаса**.

Помните, что благодаря одновременному запуску нескольких датчиков из приложения можно регулировать скорость датчика.

## <a name="low-pass-filter"></a>Фильтр нижних частот

Из-за способа обновления и вычисления значений компаса Android может потребоваться сглаживание значений. Вы можете применить _низкочастотный фильтр_, который усредняет значения синуса и косинуса углов и который можно включить с помощью перегрузки метода `Start`, принимающего параметр `bool applyLowPassFilter`:

```csharp
Compass.Start(SensorSpeed.UI, applyLowPassFilter: true);
```

Это работает только на платформе Android. На iOS и UWP параметр игнорируется.  Дополнительные сведения можно найти [здесь](https://github.com/xamarin/Essentials/pull/354#issuecomment-405316860).

--------------

## <a name="api"></a>API

- [Исходный код Compass](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Compass)
- [Документация по API Compass](xref:Xamarin.Essentials.Compass)
