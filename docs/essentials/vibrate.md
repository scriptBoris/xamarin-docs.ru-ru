---
title: 'Xamarin.Essentials: вибрация'
description: Этот документ описывает класс Вибрация в Xamarin.Essentials, который позволяет запускать и останавливать vibrate функциональные возможности для требуемого числа раз.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: ca21f43631c261cd384f9049f30f0fa29e2ca44e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855173"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: вибрация

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Вибрация** класс позволяет запускать и останавливать vibrate функциональные возможности для требуемого числа раз.

## <a name="getting-started"></a>Начало работы

Чтобы получить доступ к **вибрация** функциональность приведены следующие настройки платформы является обязательным.

# <a name="androidtabandroid"></a>[Android](#tab/android)

Разрешение Vibrate является обязательным и должен быть настроен в проекте Android. Это можно сделать одним из следующих способов:

Откройте **AssemblyInfo.cs** файл **свойства** папку и добавьте:

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

ИЛИ обновите манифест Android:

Откройте **AndroidManifest.xml** файл **свойства** папку и добавьте следующий код внутри класса **манифеста** узла.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Или щелкните правой кнопкой проект Anroid и откройте свойства проекта. В разделе **манифест Android** найти **необходимые разрешения:** области и проверьте **VIBRATE** разрешение. Будет автоматически обновлена **AndroidManifest.xml** файла.

# <a name="iostabios"></a>[iOS](#tab/ios)

Дополнительная настройка не требуется.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Дополнительная настройка не требуется.

-----

## <a name="using-vibration"></a>С помощью вибрация

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Вибрация функциональные возможности могут быть запрошены для определенного времени или по умолчанию 500 миллисекунд.

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

Можно запросить отмену вибрацией устройства с `Cancel` метод:

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

Различия не платформы.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Только подачи звуковых, если устройство имеет значение «Vibrate на кольцо».
* Всегда подачи звуковых для 500 миллисекунд.
* Не удается отменить вибрации.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Различия не платформы.

-----

## <a name="api"></a>API

- [Вибрация исходного кода](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Документация по API вибрация](xref:Xamarin.Essentials.Vibration)
