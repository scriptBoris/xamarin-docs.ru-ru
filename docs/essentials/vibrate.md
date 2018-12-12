---
title: 'Xamarin.Essentials: Vibration'
description: В этом документе описан класс Vibration в Xamarin.Essentials, который позволяет запускать и останавливать вибрацию в течение требуемого времени.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d9bf7a1e5e0d15f1fdc909745cd439115b6f8463
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898944"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: Vibration

Класс **Vibration** позволяет запускать и останавливать вибрацию в течение требуемого времени.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

Чтобы получить доступ к функции **Vibration**, нужно создать описанную ниже конфигурацию для конкретной платформы.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Требуется разрешение Vibrate (Вибрация), которое следует настроить в проекте Android. Для этого можно применить любой из следующих методов:

Откройте файл **AssemblyInfo.cs** в папке **Свойства** и добавьте в него:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

ИЛИ обновите манифест Android:

Откройте файл **AndroidManifest.xml** в папке **Properties** и добавьте приведенный ниже код в узел **manifest**.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

ИЛИ щелкните правой кнопкой мыши проект Android и откройте свойства проекта. В разделе **Манифест Android** найдите область **Требуемые разрешения:** и установите флажок для разрешения **Vibrate** (Вибрация). Это действие автоматически обновляет файл **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Различия платформ отсутствуют.

-----

## <a name="using-vibration"></a>Использование Vibration

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Возможность Vibration можно запросить для заданного промежутка времени или по умолчанию на 500 миллисекунд.

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

Отмену вибрации устройства можно запросить с помощью метода `Cancel`:

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>Различия платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Различия платформ отсутствуют.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Устройство вибрирует, только если задан параметр Vibrate on ring (Вибрация при вызове).
* Постоянная вибрация в течение 500 миллисекунд.
* Невозможно отменить вибрацию.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Различия платформ отсутствуют.

-----

## <a name="api"></a>API

- [Исходный код класса Vibration](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Документация по API вибрации](xref:Xamarin.Essentials.Vibration)
