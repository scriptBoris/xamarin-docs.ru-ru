---
title: Использование данных в приложение iOS
description: В этом документе описывается DataAccess_Adv, создать образец, который показывает, как для сбора входных данных пользователя и выполнения, чтения, обновления и удаления (CRUD) операций базы данных, в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 35caae657700e321a7560d1e95c8551b7b10a5ca
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242109"
---
# <a name="using-data-in-an-ios-app"></a>Использование данных в приложение iOS

**DataAccess_Adv** пример рабочее приложение, которое допускает ввод данных пользователем и *CRUD* функциональность базы данных (Create, Read, Update и Delete). Приложение состоит из двух экранах: список и формы ввода данных. Все код доступа к данным повторного использования, в iOS и Android без изменений.

После добавления данных экранах приложения, iOS выглядеть следующим образом:

 ![](using-data-in-an-app-images/image9.png "Список примеров iOS")

 ![](using-data-in-an-app-images/image10.png "Детализация iOS")

IOS ниже приведен проекта — код, приведенные в этом разделе содержится в **Orm** каталог:

 ![](using-data-in-an-app-images/image13.png "дерево проекта iOS")

Машинный код пользовательского интерфейса для ViewControllers в iOS выходит из области действия для этого документа.
Ссылаться на [iOS работа с таблицами и ячейками](~/ios/user-interface/controls/tables/index.md) Дополнительные сведения об элементах управления пользовательского интерфейса.

## <a name="read"></a>Чтение

Существует несколько операций чтения в примере:

-  Список для чтения
-  Чтение отдельных записей


Два метода в `StockDatabase` класса:

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

iOS обрабатывает данные, по-разному как `UITableView`.

## <a name="create-and-update"></a>Создание и обновление

Чтобы упростить код приложения, одним методом save — который выполняет вставку или обновление, в зависимости от того, была ли установлена PrimaryKey. Так как `Id` свойство помечено атрибутом `[PrimaryKey]` атрибут, его не следует задавать в коде.
Этот метод обнаружит ли значение уже предыдущих сохранены (путем проверки свойств первичного ключа) и вставить или обновить объект соответствующим образом:

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```



Реальные приложения обычно требуют некоторые проверки (например, обязательные поля, минимальная длина или другие бизнес-правила).
Хороший кросс платформенных приложений реализовать как можно большую часть проверки логической максимально в общем коде, передав ошибки проверки, резервное копирование на пользовательский Интерфейс для отображения в соответствии с возможностями платформы.

## <a name="delete"></a>Удаление

В отличие от `Insert` и `Update` методы, `Delete<T>` метод может принимать только значение первичного ключа, а не полный `Stock` объекта.
В этом примере `Stock` объект передается в метод, но только свойство Id передается на `Delete<T>` метод.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>С помощью предварительно заполненный файл базы данных SQLite

Некоторые приложения входят в состав уже заполненной базы данных.
Вы легко это можно сделать в мобильное приложение, доставки существующий файл базы данных SQLite с вашим приложением и его копирование в каталог, доступный для записи перед доступом к нему. Так как SQLite — это стандартный формат файла, используемый на многих платформах, существует ряд средств для создания файла базы данных SQLite:

-  **Расширения диспетчера SQLite Firefox** — работает на Mac и Windows и создает файлы, которые совместимы с iOS и Android.
-  **Командная строка** — см. в разделе [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


При создании файла базы данных для распространения вместе с вашим приложением, будьте внимательны с именами таблиц и столбцов, чтобы обеспечить их соответствие что код ожидает, что, особенно в том случае, если вы используете для SQLite.NET, который будет ожидать, что имена в соответствии с C# классы и свойства (или связанные пользовательские атрибуты).

Для iOS, включите файл sqlite в приложении и убедитесь, оно помечено **действие при построении: содержимого**. Поместите код в `FinishedLaunching` скопировать файл в каталог, доступный для записи *перед* вызывать любые методы данных. Следующий код копирует существующую базу данных вызывается **data.sqlite**, только в том случае, если он еще не существует.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Любой код доступа к данным (ли ADO.NET или с помощью для SQLite.NET), выполняющего это после завершения будет иметь доступ к предварительно заполненный данными.


## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS рецепты данных](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
