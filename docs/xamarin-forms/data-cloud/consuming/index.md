---
title: Использование веб-служб
description: В этом руководстве показано, как соединиться с другой веб-службы для обеспечения создания, чтения, обновления и удаления (CRUD) функциональные возможности в Xamarin.Forms приложение. Рассматриваются взаимодействует со службами ASMX, службы WCF, служб REST и мобильные приложения Azure.
ms.prod: xamarin
ms.assetid: 8B360BDA-E4E3-4A3F-9004-0E35362F49F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/20/2016
ms.openlocfilehash: a4c842ea7fd37ade9be0a9cb3e3ff7e50a6d1491
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31044578"
---
# <a name="consuming-web-services"></a>Использование веб-служб

Этот ф_изический руководстве показывается, как соединиться с другой веб-службы для обеспечения создания, чтения, обновления и удаления (CRUD) функциональные возможности в Xamarin.Forms приложение. Рассматриваются взаимодействует со службами ASMX, службы WCF, служб REST и мобильные приложения Azure.

## <a name="consuming-an-aspnet-web-service-asmxxamarin-formsdata-cloudconsumingasmxmd"></a>[Использование веб-службы ASP.NET (ASMX)](~/xamarin-forms/data-cloud/consuming/asmx.md)

Веб-службы ASP.NET (ASMX) предоставляют возможность создавать веб-служб, используемых при отправке сообщений по протоколу HTTP с помощью Simple Object Access Protocol (SOAP). SOAP — это независимая от платформы и независимые от языка протокол для создания и доступа к веб-служб. Потребителям службы ASMX, никакая о платформе, объектной модели или язык программирования, используемый для реализации службы не требуется. Они должны понимать, как отправлять и получать сообщения SOAP. В этой статье показано, как использовать к веб-службе ASMX из приложения Xamarin.Forms.

## <a name="consuming-a-windows-communication-foundation-wcf-web-servicexamarin-formsdata-cloudconsumingwcfmd"></a>[Использование веб-службы Windows Communication Foundation (WCF)](~/xamarin-forms/data-cloud/consuming/wcf.md)

WCF является единой framework корпорации Майкрософт для построения сервисноориентированных приложений. Она позволяет разработчикам создавать распределенные приложения безопасную, надежную, транзакций и с возможностью взаимодействия. Существуют различия между службами ASP.NET Web (ASMX) и WCF, но важно понимать, что WCF поддерживает те же возможности, предоставляемые ASMX — сообщения SOAP по протоколу HTTP. В этой статье показано, как использовать службы WCF SOAP в приложении Xamarin.Forms.

## <a name="consuming-a-restful-web-servicexamarin-formsdata-cloudconsumingrestmd"></a>[Использование веб-службы RESTful](~/xamarin-forms/data-cloud/consuming/rest.md)

Representational State Transfer (REST) представляет собой архитектурный стиль для построения веб-служб. ОСТАЛЬНЫЕ запросы выполняются по протоколу HTTP, используются те же глаголы HTTP, используемых веб-браузеры для получения веб-страниц и отправки данных на серверы. В этой статье показано, как использовать RESTful веб-службы из приложения Xamarin.Forms.

## <a name="consuming-an-azure-mobile-appxamarin-formsdata-cloudconsumingazuremd"></a>[Использование мобильного приложения Azure](~/xamarin-forms/data-cloud/consuming/azure.md)

Мобильные приложения Azure позволяют разрабатывать приложения с масштабируемой внутренних серверов, размещенных в службе приложений Azure, с поддержкой проверки подлинности мобильных устройств, автономной синхронизации и push-уведомлений. В этой статье, применим только для мобильных приложений Azure, использовать Node.js серверной части, описание запроса, вставки, обновления и удаления данных, хранящихся в таблице в экземпляре мобильных приложений Azure.

## <a name="related-links"></a>Связанные ссылки

- [Введение в веб-службы](~/cross-platform/data-cloud/web-services/index.md)
- [Общие сведения о поддержке асинхронного выполнения](~/cross-platform/platform/async.md)
