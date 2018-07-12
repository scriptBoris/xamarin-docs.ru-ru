---
title: 'Xamarin.Essentials: Вспомогательные функции системы файл'
description: Класс файловой системы в Xamarin.Essentials содержит ряд вспомогательных функций для поиска кэша приложения и каталоги данных и открытия файлов в пакет приложения.
ms.assetid: B3EC2DE0-EFC0-410C-AF71-7410AE84CF84
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 13293ec05261cbdc1e70fd278002d1af18654851
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815622"
---
# <a name="xamarinessentials-file-system-helpers"></a>Xamarin.Essentials: Вспомогательные функции системы файл

![Предварительные версии NuGet](~/media/shared/pre-release.png)

**Файловой системы** класс содержит ряд вспомогательных функций для поиска приложения кэша и каталоги данных и открытия файлов в пакет приложения.

## <a name="using-file-system-helpers"></a>Использование вспомогательных методов системы файла

Добавьте ссылку на Xamarin.Essentials в классе:

```csharp
using Xamarin.Essentials;
```

Чтобы получить каталог приложения для хранения **кэшировать данные**. Данные кэша можно использовать для любых данных, который требуется сохранить больше, чем временные данные, но не должно быть данные, необходимые для правильной работы.

```csharp
var cacheDir = FileSystem.CacheDirectory;
```

Для получения каталогов верхнего уровня приложения для всех файлов, не являющихся файлами данных пользователя. Эти файлы архивируются в операционную систему синхронизации framework. См. в разделе ниже особенности реализации платформы.

```csharp
var mainDir = FileSystem.AppDataDirectory;
```

Чтобы открыть файл, который входит в пакет приложения:

```csharp
 using (var stream = await FileSystem.OpenAppPackageFileAsync(templateFileName))
 {
    using (var reader = new StreamReader(stream))
    {
        var fileContents = await reader.ReadToEndAsync();
    }
 }
```

## <a name="platform-implementation-specifics"></a>Особенности реализации платформы

# <a name="androidtabandroid"></a>[Android](#tab/android)

- **CacheDirectory** — возвращает [CacheDir](https://developer.android.com/reference/android/content/Context.html#getCacheDir) текущего контекста.
- **AppDataDirectory** — возвращает [FilesDir](https://developer.android.com/reference/android/content/Context.html#getFilesDir) текущего контекста и резервное копирование с использованием [Автоматическая архивация](https://developer.android.com/guide/topics/data/autobackup.html) начиная на API 23 и более поздних версий.

Добавить любой файл в **активы** папку в Android проект и пометить как действие при построении **AndroidAsset** на работу с `OpenAppPackageFileAsync`.

# <a name="iostabios"></a>[iOS](#tab/ios)

- **CacheDirectory** — возвращает [библиотеки/кэши](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) каталога.
- **AppDataDirectory** — возвращает [библиотеки](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) каталога, подвергаемого резервному копированию, iTunes и iCloud.

Добавить любой файл в **ресурсы** папку в iOS проект и пометить как действие при построении **BundledResource** на работу с `OpenAppPackageFileAsync`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

- **CacheDirectory** — возвращает [LocalCacheFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localcachefolder#Windows_Storage_ApplicationData_LocalCacheFolder) каталог...
- **AppDataDirectory** — возвращает [LocalFolder](https://docs.microsoft.com/en-us/uwp/api/windows.storage.applicationdata.localfolder#Windows_Storage_ApplicationData_LocalFolder) каталога, подвергаемого резервному копированию в облако.

Добавить любой файл в корневой каталог в проекте UWP и пометить как действие при построении **содержимого** на работу с `OpenAppPackageFileAsync`.

--------------

## <a name="api"></a>API

- [Исходный код файла вспомогательных функций системы](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/FileSystem)
- [Документация по API системы файла](xref:Xamarin.Essentials.FileSystem)
