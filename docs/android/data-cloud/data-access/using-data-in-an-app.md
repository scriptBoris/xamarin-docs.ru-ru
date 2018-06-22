---
title: Использование данных в приложение Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/08/2018
ms.openlocfilehash: b79b2e44e79a6ff75b096c7443f6d46c20e27144
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/20/2018
ms.locfileid: "31647038"
---
# <a name="using-data-in-an-app"></a>С помощью данных в приложении

**DataAccess_Adv** приведен пример рабочее приложение, которое позволяет вводу данных пользователем и функциональные возможности CRUD (Создание, чтение, обновление и удаление) базы данных. Приложение состоит из двух экранах: список и форму для ввода данных. Все код доступа к данным многократно используемые в iOS и Android без изменений.

После добавления данных приложение экраны, Android выглядеть следующим образом:

![Список примеров Android](using-data-in-an-app-images/image11.png "Android пример списка")

![Android детализация](using-data-in-an-app-images/image12.png "Android детализация")

Ниже показан проект Android &ndash; код, приведенные в этом разделе содержится в **Orm** каталога:

![Дерево проекта Android](using-data-in-an-app-images/image14.png "дерева проекта Android")

Машинный код пользовательского интерфейса для выполнения действий в Android находится вне области рассмотрения этого документа. Ссылаться на [Android представлениях ListView и адаптеры](~/android/user-interface/layouts/list-view/index.md) Дополнительные сведения об элементах управления пользовательского интерфейса.

## <a name="read"></a>Чтение

Существует несколько операций чтения в образце:

-  При чтении списка
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

Чтобы упростить код приложения, одним методом save — предоставленный, который выполняет инструкции Insert или Update, в зависимости от того, была ли установлена PrimaryKey. Поскольку `Id` свойство помечено `[PrimaryKey]` атрибута, его не следует задавать в коде. Этот метод проверяет ли это значение было предыдущего сохранены (проверив свойство первичного ключа) и вставить или обновить объект соответствующим образом:

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

Некоторые проверки (например, обязательные поля, минимальная длина или другие бизнес-правила) обычно требуют реальных приложений. Хороший кросс платформенных приложений реализовать столько проверки логической максимально общего кода, передавая ошибки проверки, создать резервную копию пользовательский Интерфейс для отображения в соответствии с возможностями платформы.

## <a name="delete"></a>Удаление

В отличие от `Insert` и `Update` методы, `Delete<T>` метод может принимать только значения первичного ключа, а не полный `Stock` объекта. В этом примере `Stock` объект передается в метод, но только свойство идентификатора передается в `Delete<T>` метод.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>С помощью предварительно заполненных SQLite файла базы данных

Некоторые приложения поставляются вместе с базой данных, уже заполненной данными. Вы можете легко этого в мобильное приложение доставки существующий файл базы данных SQLite вместе с приложением и скопировать его для записи каталога перед доступом к нему. Поскольку SQLite стандартные формате, который используется на многих платформах, существует ряд средств, доступных для создания файла базы данных SQLite.

-   **Расширения диспетчера SQLite Firefox** &ndash; работает на Mac и Windows и создает файлы, совместимые с iOS и Android.

-   **Команда** &ndash; разделе [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

При создании файла базы данных для распространения вместе с приложением, будьте внимательны с именования таблиц и столбцов, чтобы они соответствуют ожиданиям кода, особенно в том случае, если вы используете SQLite.NET, который будет ожидать, что имена для сопоставления C# классы и свойства (или связанные пользовательские атрибуты).

Убедитесь, что код запускается перед все остальное в приложении Android, можно поместить его в первое действие для загрузки или можно создать `Application` подкласс, в котором загружается перед любые действия. Ниже показан код `Application` подкласс, в котором копирует существующий файл базы данных **data.sqlite** из **/Resources/Raw/** каталога.

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
- [Рецепты данных для Android](https://developer.xamarin.com/recipes/android/data/)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
