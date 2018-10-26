---
title: Введение в поставщиков содержимого
description: ОС Android использует поставщики содержимого для упрощения доступа к общим данным, например файлы мультимедиа, контакты и календари. В этой статье класс поставщик содержимого, а также приведены два примера способы его использования.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 4105200c48e41b142fc71e3a524023790b683cdb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105991"
---
# <a name="intro-to-contentproviders"></a>Введение в поставщиков содержимого

_ОС Android использует поставщики содержимого для упрощения доступа к общим данным, например файлы мультимедиа, контакты и календари. В этой статье класс поставщик содержимого, а также приведены два примера способы его использования._


## <a name="content-providers-overview"></a>Общие сведения о поставщиках содержимого

Объект *поставщик содержимого* инкапсулирует хранилищем данных и предоставляет API для доступа к нему. Поставщик существует как часть приложения Android, которая обычно предоставляет пользовательский Интерфейс для отображения и управления данными. Основное преимущество использования поставщика содержимого позволяет легко получить доступ к инкапсулированные данные, с помощью клиентского объекта поставщика в других приложениях (вызывается *ContentResolver*). Вместе поставщика содержимого и сопоставителя содержимого предоставляют согласованные между приложениями API для доступа к данным, легко создавать и использовать. Любое приложение, можно использовать `ContentProviders` для внутренних целей управления данными и предоставить его для других приложений.

Объект `ContentProvider` также является обязательным для приложения для предоставления пользовательского поиска предложений, или если вы хотите предоставить возможность копирования сложных данных из приложения, чтобы вставить в другие приложения. В этом документе показано, как для доступа и разработки `ContentProviders` с помощью Xamarin.Android.

Структура в этом разделе выглядит следующим образом:

- **Принцип работы** &ndash; обзорные сведения о том, что `ContentProvider` предназначен для, и как она работает.

- **Использование поставщика содержимого** &ndash; пример доступа к списку контактов.

- **Используя поставщик содержимого для совместного использования данных** &ndash; написанием и много `ContentProvider` в одном приложении.

`ContentProviders` и курсоры, которые оперируют свои данные часто используются для заполнения ListViews. Ссылаться на [руководство по ListView и адаптерам](~/android/user-interface/layouts/list-view/index.md) Дополнительные сведения об использовании этих классов.

`ContentProviders` предоставляемые Android (или других приложений) — простой способ включить данные из других источников в приложении. Они позволяют доступа и представления данных, такие как список контактов, фотографии или календарь событий из приложения и позволяют пользователю взаимодействовать с этими данными.

Пользовательские `ContentProviders` являются удобным способом упаковки данных для использования внутри свое собственное приложение, или для использования другими приложениями (в том числе специального использования пользовательского поиска и копирования и вставки).

В этом разделе представлены некоторые простые примеры использования и написание `ContentProvider` кода.



## <a name="related-links"></a>Связанные ссылки

- [Демонстрация ContactsAdapter (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (пример)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Руководство для разработчиков поставщиков содержимого](http://developer.android.com/guide/topics/providers/content-providers.html)
- [Ссылки на поставщик содержимого-класс](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Ссылки на класс ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Ссылки на класс ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Ссылки на класс CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Ссылки на класс UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Ссылки на класс ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
