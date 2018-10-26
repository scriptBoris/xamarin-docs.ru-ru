---
title: Использование ContentProvider
ms.prod: xamarin
ms.assetid: 251F7557-328D-0132-F39D-595920A28B87
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: d1ec628de3481820f320a5a8e6ef88fcbaab75a6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122118"
---
# <a name="using-a-contentprovider"></a>Использование ContentProvider

CursorAdapters также может использоваться для отображения данных из ContentProvider.
Поставщиков содержимого позволяют получить доступ к данным, доступных в других приложениях (включая Android системные данные, такие как контакты, мультимедиа и информации календаря).

С помощью LoaderManager CursorLoader является предпочтительным способом доступа к ContentProvider. LoaderManager появился в Android 3.0 (уровень API 11, Honeycomb) для перемещения блокирующих задачах вне основного потока, и с помощью CursorLoader позволяет данные загружаются в потоке перед привязанным к ListView для отображения.

Ссылаться на [введение поставщиков содержимого](~/android/platform/content-providers/index.md) Дополнительные сведения.

