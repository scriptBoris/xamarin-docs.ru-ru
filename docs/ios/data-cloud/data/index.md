---
title: Доступ к данным Xamarin.iOS
description: Этот документ содержит ссылки на руководства, описывающие способы работы с локальными базами данных в приложении Xamarin.iOS. Связанное содержимое рассматриваются для SQLite.NET, ADO.NET и многое другое.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 10/11/2016
ms.openlocfilehash: 017118a74528718ea99fe218f443b8df83d7c52e
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241283"
---
# <a name="xamarinios-data-access"></a>Доступ к данным Xamarin.iOS

Xamarin.iOS поддерживает интерфейсы API для доступа к базе данных, такие как:

-  Платформа ADO.NET.
-  SQLite-NET сторонний библиотеки.

Это руководство содержит обзор баз данных в целом перед описанием Настройка ADO.NET и для SQLite.NET для доступа к базам данных SQLite в приложениях Xamarin.iOS. 

Большая часть кода в этом документе полностью кросс платформенные и будет выполняться в iOS или Android без изменений. Существует два примеры приложений, которые рассматриваются:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) — простых операций записывает результаты в текстовый отображения элемента управления;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) — объединяет операции с данными в небольших рабочее приложение, которое перечисляет и изменяет простая структура данных.

Оба примера решения содержат iOS и Android примеры проектов приложений.

Приложения Xamarin.Forms. в статье [работы с базами данных](~/xamarin-forms/app-fundamentals/databases.md) которой описана работа с SQLite в библиотеке переносимой библиотеки Классов с помощью Xamarin.Forms.

## <a name="sections"></a>Разделы

-  [Введение](introduction.md)
-  [Конфигурация](configuration.md)
-  [Использование ORM для SQLite.NET](using-sqlite-orm.md)
-  [Использование ADO.NET](using-adonet.md)
-  [Использование данных в приложении](using-data-in-an-app.md)

## <a name="summary"></a>Сводка

В этой главе рассматривается доступ к данным в Xamarin.iOS с помощью SQLite в качестве ядра базы данных. Базы данных может осуществляться в «напрямую» с использованием синтаксиса ADO.NET, или можно включить ORM для SQLite.NET и выполнения операций с данными в C#.

Мы рассмотрели два примера: содержит код доступа к данным очень простой выходные данные в текстовое поле, а также простое приложение, которое включает в себя создания, чтения, обновления и удаления функциональные возможности. Мы также описана работа с потоками и способе направления разбора приложения с помощью предварительно заполненный база данных SQLite.

Дополнительные примеры доступа к данным платформах см. в нашем [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) практический пример.

## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS рецепты данных](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
