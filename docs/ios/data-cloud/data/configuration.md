---
title: Настройка SQLite в Xamarin.iOS
description: В этом документе описывается, как определить расположение для файла базы данных SQLite в приложении Xamarin.iOS. Эти понятия относятся независимо от того, механизм доступа выбранных данных.
ms.prod: xamarin
ms.assetid: E5582F4B-AD74-420F-9E6D-B07CFB420B3A
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: ce9822b9c6c6a360f8c289f46d4d777d80b898e1
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114981"
---
# <a name="configuring-sqlite-in-xamarinios"></a>Настройка SQLite в Xamarin.iOS

Использовать SQLite в приложении Xamarin.iOS, вам потребуется определить правильное расположение файла для файла базы данных.

## <a name="database-file-path"></a>Путь к файлу базы данных

Независимо от того, какой используется метод доступа к данным необходимо создать файл базы данных, прежде чем данные могут храниться с SQLite. В зависимости от целевой платформы будет отличаться нужный файл. Для iOS класс среды можно использовать для создания допустимым путем, как показано в следующем фрагменте кода:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Имеется ряд других вещей, необходимо учитывать при выборе места для хранения файла базы данных. На устройствах iOS можно базы данных, чтобы резервные копии автоматически (или нет).

Если вы хотите использовать в другое место на каждой платформе в кроссплатформенного приложения можно использовать директивы компилятора как показано для создания другой путь для каждой платформы:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1+ to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Ссылаться на [работа с файловой системой](~/ios/app-fundamentals/file-system.md) для получения дополнительной информации, на какие расположения файлов для использования на устройствах iOS. См. в разделе [Создание межплатформенных приложений платформы](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) Дополнительные сведения об использовании директивы компилятора для написание кода специально для каждой платформы.

## <a name="threading"></a>Потоки

Не следует использовать то же подключение базы данных SQLite в нескольких потоках. Следите за тем открыть, использовать и затем закройте все подключения, которые создаются на том же потоке.

Чтобы убедиться, что ваш код не пытается получить доступ к базе данных SQLite из нескольких потоков одновременно, вручную установить блокировку всякий раз, когда вы будете обращаться к базе данных, следующим образом:

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Все доступа к базе данных (операций чтения, записи, обновления, и т.д) должен быть заключен в ту же блокировку. Будьте осторожны, гарантируя, что работа в предложении блокировки хранится простой и не вызывать другие методы, которые также могут установить блокировку во избежание взаимоблокировки!


## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS рецепты данных](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
