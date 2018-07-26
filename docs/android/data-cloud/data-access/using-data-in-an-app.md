---
title: Использование данных в приложение Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: 563c04ef1c8eec00108844894c5f9bdc0e9950e3
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241894"
---
# <a name="using-data-in-an-app"></a>Использование данных в приложение

**DataAccess_Adv** пример рабочее приложение, которое допускает ввод данных пользователем и функциональные возможности базы данных CRUD (Create, Read, Update и Delete). Приложение состоит из двух экранах: список и формы ввода данных. Все код доступа к данным повторного использования, в iOS и Android без изменений.

После добавления данных экранах приложения, Android выглядеть следующим образом:

![Список примеров Android](using-data-in-an-app-images/image11.png "список примеров Android")

![Android детализация](using-data-in-an-app-images/image12.png "Android детализация")

Ниже показан проект Android &ndash; код, приведенные в этом разделе содержится в **Orm** каталог:

![Дерево проекта Android](using-data-in-an-app-images/image14.png "дерева проекта Android")

Машинный код пользовательского интерфейса для действий в Android выходит из области действия для этого документа. Ссылаться на [Android ListView и адаптерам](~/android/user-interface/layouts/list-view/index.md) Дополнительные сведения об элементах управления пользовательского интерфейса.

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

Android отображает данные в виде `ListView`.

## <a name="create-and-update"></a>Создание и обновление

Чтобы упростить код приложения, одним методом save — который выполняет вставку или обновление, в зависимости от того, была ли установлена PrimaryKey. Так как `Id` свойство помечено атрибутом `[PrimaryKey]` атрибут, его не следует задавать в коде. Этот метод обнаружит ли значение уже предыдущих сохранены (путем проверки свойств первичного ключа) и вставить или обновить объект соответствующим образом:

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

Реальные приложения обычно требуют некоторые проверки (например, обязательные поля, минимальная длина или другие бизнес-правила). Хороший кросс платформенных приложений реализовать как можно большую часть проверки логической максимально в общем коде, передав ошибки проверки, резервное копирование на пользовательский Интерфейс для отображения в соответствии с возможностями платформы.

## <a name="delete"></a>Удаление

В отличие от `Insert` и `Update` методы, `Delete<T>` метод может принимать только значение первичного ключа, а не полный `Stock` объекта. В этом примере `Stock` объект передается в метод, но только свойство Id передается на `Delete<T>` метод.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>С помощью предварительно заполненный файл базы данных SQLite

Некоторые приложения входят в состав уже заполненной базы данных. Вы легко это можно сделать в мобильное приложение, доставки существующий файл базы данных SQLite с вашим приложением и его копирование в каталог, доступный для записи перед доступом к нему. Так как SQLite — это стандартный формат файла, используемый на многих платформах, существует ряд средств для создания файла базы данных SQLite:

-   **Расширения диспетчера SQLite Firefox** &ndash; работает на Mac и Windows и создает файлы, которые совместимы с iOS и Android.

-   **Командная строка** &ndash; см. в разделе [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

При создании файла базы данных для распространения вместе с вашим приложением, будьте внимательны с именами таблиц и столбцов, чтобы обеспечить их соответствие что код ожидает, что, особенно в том случае, если вы используете для SQLite.NET, который будет ожидать, что имена в соответствии с C# классы и свойства (или связанные пользовательские атрибуты).

Чтобы обеспечить возможность выполнения кода перед другими компонентами в ваше приложение Android, его можно поместить в первое действие, чтобы загрузить или создать `Application` подкласс, в котором загружается до любых действий. В коде ниже показан `Application` подкласс, который копирует существующий файл базы данных **data.sqlite** из **/Resources/Raw/** каталога.

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```


## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Рецепты данных для Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
