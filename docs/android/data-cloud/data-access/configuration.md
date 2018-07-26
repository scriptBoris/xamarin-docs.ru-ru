---
title: Конфигурация
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 10/11/2016
ms.openlocfilehash: b3a7858361d25f26807ea328e8bfdd30ca8d483b
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241883"
---
# <a name="configuration"></a>Конфигурация

Использовать SQLite в приложении Xamarin.Android, будет необходимо определить правильное расположение файла для файла базы данных.

## <a name="database-file-path"></a>Путь к файлу базы данных

Независимо от того, какой используется метод доступа к данным необходимо создать файл базы данных, прежде чем данные могут храниться с SQLite. В зависимости от целевой платформы будет отличаться нужный файл. Для Android класс среды можно использовать для создания допустимым путем, как показано в следующем фрагменте кода:

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Имеется ряд других вещей, необходимо учитывать при выборе места для хранения файла базы данных. Например в Android вы можете необходимость использования внутреннего или внешнего хранилища.

Если вы хотите использовать в другое место на каждой платформе в кроссплатформенного приложения можно использовать директивы компилятора как показано для создания другой путь для каждой платформы:

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Указания по использованию файловой системы в Android, см. в разделе [Обзор файлов](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files) рецепт. См. в разделе [Создание межплатформенных приложений платформы](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) Дополнительные сведения об использовании директивы компилятора для написание кода специально для каждой платформы.

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
- [Рецепты данных для Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
