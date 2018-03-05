---
title: "Данные и облачные службы"
description: "Xamarin.Forms приложений можно использовать веб-службы, реализовано с помощью широкий спектр технологий, а в этом руководстве рассмотрим, как это сделать."
ms.topic: article
ms.prod: xamarin
ms.assetid: "0601D9D0-C8D2-4C3B-A749-A340BDBF64A4ß"
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 28007ee702c66f3b819430b544465d3470d571d9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/27/2018
---
# <a name="data--cloud-services"></a>Данные и облачные службы

_Xamarin.Forms приложений можно использовать веб-службы, реализовано с помощью широкий спектр технологий, а в этом руководстве рассмотрим, как это сделать._

Общие сведения для использования службы кросс платформенных веб на платформе Xamarin см. в разделе [введение в веб-службы](~/cross-platform/data-cloud/web-services/index.md).

## <a name="understanding-the-samplexamarin-formsdata-cloudwalkthroughmd"></a>[Обзор выборки](~/xamarin-forms/data-cloud/walkthrough.md)

В этой статье Пошаговое руководство по Xamarin.Forms образец приложения, демонстрирующий, как соединиться с другой веб-службы. Рассматриваются составляющие приложение, страниц, модели данных и вызова операций веб-службы.

## <a name="consuming-web-servicesxamarin-formsdata-cloudconsumingindexmd"></a>[Использование веб-служб](~/xamarin-forms/data-cloud/consuming/index.md)

В этом руководстве показано, как соединиться с другой веб-службы для обеспечения создания, чтения, обновления и удаления (CRUD) функциональные возможности в Xamarin.Forms приложение. Рассматриваются связи с [служб ASMX](consuming/asmx.md), [служб WCF](consuming/wcf.md), [службы REST](consuming/rest.md), [мобильных приложений Azure](consuming/azure.md)и [ Amazon Web Services](consuming/aws.md).

## <a name="authenticating-access-to-web-servicesxamarin-formsdata-cloudauthenticationindexmd"></a>[Проверка подлинности доступа к веб-службам](~/xamarin-forms/data-cloud/authentication/index.md)

В этом руководстве объясняется, как интегрировать службы проверки подлинности в приложении Xamarin.Forms позволяет пользователям общий доступ к серверной части только Имея доступ к собственным данным. Рассматриваются [с обычной проверкой подлинности с помощью службы REST](authentication/rest.md), [с помощью компонента Xamarin.Auth для проверки подлинности OAuth Поставщики удостоверений](authentication/oauth.md)и с помощью встроенных проверки подлинности механизмы, предлагаемые [мобильных приложений Azure](authentication/azure.md), и [Amazon Web Services](authentication/aws.md).

## <a name="synchronizing-data-with-web-servicessyncindexmd"></a>[Синхронизация данных с веб-службами](sync/index.md)

В этой статье описывается добавление автономной синхронизации функциональные возможности в Xamarin.Forms приложения. Автономная синхронизация позволяет пользователям взаимодействовать с мобильного приложения, просмотр, добавление или изменение данных, даже в том случае, где нет сетевого подключения. Изменения сохраняются в локальной базе данных, и когда устройство подключено к сети, можно синхронизировать изменения с веб-службой.

## <a name="sending-push-notificationspush-notificationsindexmd"></a>[Отправка push-уведомлений](push-notifications/index.md)

В этой статье показано, как добавить push-уведомлений в приложение Xamarin.Forms. Концентраторы уведомлений Azure предоставляют масштабируемые push-инфраструктуру для отправки мобильные push-уведомления с любого сервера на любую мобильную платформу, устраняя необходимость связываться с разных систем уведомлений платформы серверной части сложности.

## <a name="storing-files-in-the-cloudstorageindexmd"></a>[Хранение файлов в облаке](storage/index.md)

В этой статье демонстрируется использование Xamarin.Forms для хранения текст и двоичные данные в хранилище Azure и способах доступа к данным. Хранилища Azure — это решение масштабируемые облачные хранилища, который может использоваться для хранения данных, структурированные и неструктурированные.

## <a name="searching-data-in-the-cloudsearchindexmd"></a>[Поиск данных в облаке](search/index.md)

В этой статье демонстрируется использование библиотеки поиска Microsoft Azure для интеграции поиска Azure в приложении Xamarin.Forms. Поиск Azure — облачной службы, которая предоставляет индексирования и запросов возможности загруженные данные. Требования к инфраструктуре и сложности алгоритма поиска, обычно связанные с реализации функциональности поиска в приложении будут удалены.

## <a name="storing-data-in-a-document-databasecosmosdbindexmd"></a>[Хранение данных в базе данных документов](cosmosdb/index.md)

В этом руководстве демонстрируется использование клиентской библиотеки Microsoft Azure DocumentDB интегрировать базе данных Azure Cosmos DB документа в приложении Xamarin.Forms. База данных документа Azure Cosmos DB является NoSQL базы данных, которая предоставляет высокоскоростной доступ к документам JSON, предоставляющие доступ к службе быстрое высокодоступные, масштабируемые базы данных для приложений, требующих удобный масштабирования и глобальные репликации.

## <a name="adding-intelligence-with-cognitive-servicescognitive-servicesindexmd"></a>[Добавление аналитики с помощью Cognitive Services](cognitive-services/index.md)

В этом руководстве объясняется, как использовать некоторые API-интерфейсов Когнитивных служб Майкрософт в приложении Xamarin.Forms. Когнитивных службы — это набор API-интерфейсов, пакеты SDK и службы, доступные разработчикам, чтобы сделать свои приложения более разумно, добавляя такие возможности, например распознавание лиц, распознавание речи и основные сведения о языке.