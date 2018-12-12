---
title: 'Xamarin.Essentials: Preferences'
description: В этом документе описан класс Preferences в Xamarin.Essentials, который сохраняет параметры приложения в хранилище ключей и значений. Здесь рассматривается использование класса и типы данных, которые можно сохранить.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 0144cdd4e12d53e0ac82af24a932825491db9e41
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52899088"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: Preferences

Класс **Preferences** помогает хранить параметры приложения в хранилище ключей и значений.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>Использование Preferences

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы сохранить значение определенного _ключа_ в параметрах, используйте следующий код:

```csharp
Preferences.Set("my_key", "my_value");
```

Чтобы извлечь значение из настроек или значение по умолчанию (если значение не задано в настройках):

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Чтобы удалить _ключ_ из параметров, используйте следующий код:

```csharp
Preferences.Remove("my_key");
```

Чтобы удалить все параметры, используйте следующий код:

```csharp
Preferences.Clear();
```

В дополнение к этим методам каждый принимает необязательное свойство `sharedName`, которое может использоваться для создания дополнительных контейнеров для параметров. Особенности реализации для платформ см. ниже.

## <a name="supported-data-types"></a>Поддерживаемые типы данных

Следующие типы данных поддерживаются в классе **Preferences**:

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>Сведения о реализации

Значения `DateTime` хранятся в двоичном 64-битном формате (длинное целое число) с использованием двух методов, определяемых классом `DateTime`. Метод [`ToBinary`](xref:System.DateTime.ToBinary) используется для шифрования значения `DateTime`. Метод [`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) декодирует значение. Ознакомьтесь с документацией об этих методах для возможной корректировки декодированных значений, если сохраняется значение `DateTime` не в формате UTC.

## <a name="platform-implementation-specifics"></a>Особенности реализации для платформ

# <a name="androidtabandroid"></a>[Android](#tab/android)

Все данные хранятся в объекте [SharedPreferences](https://developer.android.com/training/data-storage/shared-preferences.html). Если свойство `sharedName` не указано, используются общие параметры по умолчанию, в противном случае используется имя для получения **частных** общих параметров с указанным именем.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/xamarin/ios/app-fundamentals/user-defaults) используются для хранения значений на устройствах iOS. Если свойство `sharedName` не указано, используется `StandardUserDefaults`. В противном случае используется имя для создания `NSUserDefaults` с определенным именем, используемым для `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) используется для хранения значений на устройстве. Если свойство `sharedName` не указано, используется `LocalSettings`. В противном случае используется имя для создания контейнера в `LocalSettings`.

--------------

## <a name="persistence"></a>Сохраняемость

При удалении приложения все _параметры_ удаляются. Существует одно исключение: приложения, которые выполняются на платформе Android 6.0 (API уровня 23) или более поздних версий и используют [__автоматическое резервное хранение__](https://developer.android.com/guide/topics/data/autobackup). Эта функция включена по умолчанию и сохраняет данные приложения, включая __общие параметры__, которые использует API **параметров**. Ее можно отключить, выполнив следующие шаги в [документации Google](https://developer.android.com/guide/topics/data/autobackup).

## <a name="limitations"></a>Ограничения

При сохранении строки этот API сохраняет небольшие блоки текста.  Производительность может быть низкой, если сохранять большие объемы текста.

## <a name="api"></a>API

- [Исходный код класса Preferences](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Документация по API параметров](xref:Xamarin.Essentials.Preferences)
