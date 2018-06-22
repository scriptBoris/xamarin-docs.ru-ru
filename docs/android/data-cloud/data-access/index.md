---
title: Доступ к данным Xamarin.Android
description: Большинство приложений имеют некоторые требования, чтобы сохранить данные на устройстве локально. Если объем данных элементарно мал, это обычно требуется базы данных и уровнем данных в приложении для управления доступом к базе данных.  Android имеет ядро базы данных SQLite «встроенные» и доступа для хранения и извлечения данных стало проще благодаря платформе Xamarin. В этом документе показано, как получить доступ к базе данных SQLite образом кросс платформенных.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 508a8f54723bfdd30b1c8ea8d4b6c1d422ae24e9
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30763254"
---
# <a name="xamarinandroid-data-access"></a>Доступ к данным Xamarin.Android

_Большинство приложений имеют некоторые требования, чтобы сохранить данные на устройстве локально. Если объем данных элементарно мал, это обычно требуется базы данных и уровнем данных в приложении для управления доступом к базе данных.  Android имеет ядро базы данных SQLite «встроенные» и доступа для хранения и извлечения данных стало проще благодаря платформе Xamarin. В этом документе показано, как получить доступ к базе данных SQLite образом кросс платформенных._

## <a name="data-access-overview"></a>Обзор доступа к данным

Большинство приложений имеют некоторые требования, чтобы сохранить данные на устройстве локально. Если объем данных элементарно мал, это обычно требуется базы данных и уровнем данных в приложении для управления доступом к базе данных. Android оба имеет ядро базы данных Sqlite «встроенные» и доступ к данным стало проще благодаря платформе Xamarin которая поставляется с поставщиком данных SQLite.

Xamarin.Android поддерживает интерфейсы API доступа к базе данных, такие как:

-  Платформа ADO.NET.
-  SQLite NET сторонний библиотеки.

Большую часть кода в этом разделе, полностью кросс платформенных и будет выполняться на iOS или Android без изменения. Существуют два образца приложения, рассматриваются:

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; простых операций записывает результаты в текстовый отображения элемента управления;

-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; интегрирует операций с данными в небольших рабочее приложение, которое содержит список и изменяет простая структура данных.

Оба образца решения содержат iOS и Android образцы проектов приложений.

Приложения Xamarin.Forms чтения [работа с базами данных](~/xamarin-forms/app-fundamentals/databases.md) объясняет, как работать с SQLite библиотеки PCL с помощью Xamarin.Forms.

В этом разделе описывается доступ к данным в Xamarin.Android, с помощью SQLite как компонент database engine. Базы данных может осуществляться «напрямую», используя синтаксис ADO.NET, или можно включить SQLite.NET ORM и выполнения операций с данными в C#.

Рассматриваются две выборки: один содержит код доступа к данным очень простого, выходные данные для текстового поля и простое приложение, которое включает в себя создания, чтения, обновления и удаления функциональные возможности. Также рассматривается потоки и как в качестве начального приложения с предварительно заполненную базу данных SQLite.

Дополнительные примеры доступа к данным на разных платформах см. наш [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) практический пример.


## <a name="related-links"></a>Связанные ссылки

- [DataAccess Basic (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess Advanced (пример)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Рецепты данных для Android](https://developer.xamarin.com/recipes/android/data/)
- [Доступ к данным Xamarin.Forms](~/xamarin-forms/app-fundamentals/databases.md)
