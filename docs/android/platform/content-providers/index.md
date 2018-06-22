---
title: Введение в ContentProviders
description: ОС Android использует поставщики содержимого для упрощения доступа к общим данным, таких как файлы мультимедиа, контакты и календарь сведения. В этой статье представляет класс поставщик содержимого и приводятся два примера способ его использования.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: e534c02820bfeab3a5bc1211bf0cbb20b9821af3
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764168"
---
# <a name="intro-to-contentproviders"></a>Введение в ContentProviders

_ОС Android использует поставщики содержимого для упрощения доступа к общим данным, таких как файлы мультимедиа, контакты и календарь сведения. В этой статье представляет класс поставщик содержимого и приводятся два примера способ его использования._


## <a name="content-providers-overview"></a>Обзор поставщиков содержимого

Объект *поставщик содержимого* инкапсулирует хранилищем данных и предоставляет API-Интерфейс для доступа к нему. Поставщик существует как часть приложения Android, которое обычно также предоставляет пользовательский Интерфейс для отображения и управления данными. Основное преимущество использования поставщика содержимого позволяет другим приложениям легко получить доступ к инкапсулированные данные с помощью клиентского объекта поставщика (называется *ContentResolver*). Вместе поставщик содержимого и Сопоставитель обеспечивают согласованность между приложениями API для доступа к данным, который прост для создания и использования. Любое приложение может быть использован `ContentProviders` для внутренних целей управления данными и предоставлять его в других приложениях.

Объект `ContentProvider` также является обязательным для приложения для предоставления пользовательского поиска предложений, или если вы хотите предоставить возможность скопировать сложные данные из приложения, чтобы вставить в другие приложения. В этом документе показано, как доступа и разработки `ContentProviders` с Xamarin.Android.

Структура этого раздела выглядит следующим образом:

- **Принцип работы** &ndash; Общие сведения о том, что `ContentProvider` предназначена для, и как она работает.

- **Использование поставщика содержимого** &ndash; пример доступа к списку контактов.

- **Используя поставщик содержимого для совместного использования данных** &ndash; написанием и много `ContentProvider` в одном приложении.

`ContentProviders` и курсоры, которые работают на своих данных часто используются для заполнения представлениях ListView. Ссылаться на [руководство по представлениях ListView и адаптеров](~/android/user-interface/layouts/list-view/index.md) Дополнительные сведения об использовании этих классов.

`ContentProviders` предоставляемые Android (или другие приложения) — это простой способ включения данных из других источников в приложении. Они позволяют получить доступ и представляют данные, такие как список контактов, фото или события календаря из приложения и позволяют работать с данными.

Пользовательские `ContentProviders` — удобный способ упаковки данных для использования внутри собственного приложения или для использования другими приложениями (включая специального использования пользовательского поиска и копирования и вставки).

В этом разделе представлены некоторые простые примеры использования и записи `ContentProvider` кода.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрация ContactsAdapter (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Руководство для разработчиков поставщиков содержимого](http://developer.android.com/guide/topics/providers/content-providers.html)
- [Поставщик содержимого по классам](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Ссылку на класс ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Ссылку на класс ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Ссылку на класс CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Ссылку на класс UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Ссылку на класс ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
