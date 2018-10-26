---
title: SiriKit в Xamarin.iOS
description: В этой статье показано, как использовать SiriKit в приложении Xamarin.iOS для предоставления служб, доступных для пользователя с помощью Siri на устройстве iOS.
ms.prod: xamarin
ms.assetid: 84E5681A-F557-4967-AA99-F831169157AA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 9f7cbb3f7d9e448947ec8163a8660616910e750f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117529"
---
# <a name="sirikit-in-xamarinios"></a>SiriKit в Xamarin.iOS

_В этой статье показано, как использовать SiriKit в приложении Xamarin.iOS для предоставления служб, доступных для пользователя с помощью Siri на устройстве iOS._

Новое в iOS 10, SiriKit позволяет приложению iOS для предоставления служб, доступных для пользователя с помощью Siri и приложения карты на устройстве iOS с помощью расширения приложений и новый **Intents** и **интерфейса Intents UI** платформ.

Siri работает с концепцией **домены**, групп знать действия для связанных задач. Каждое взаимодействие с приложения с Siri необходимо относятся к одной из своих доменов известной службы следующим образом:

- Аудио- или видеосвязь.
- Резервирования поездки.
- Управление тренировок.
- Обмен сообщениями.
- Поиск фотографий.
- Отправлять или получать платежи.

Когда пользователь выполняет запрос Siri, связанных с одним расширением приложения служб, SiriKit отправляет расширение **намерение** объект, который описывает запрос пользователя, а также все дополнительные данные. Расширение приложения затем создает соответствующий **ответа** объекта для заданного **намерение**, о том, как расширение может обработать запрос.

## <a name="understanding-sirikit-conceptsiosplatformsirikitunderstanding-sirikitmd"></a>[Основные сведения о понятиях SiriKit](~/ios/platform/sirikit/understanding-sirikit.md)

В этой статье рассматриваются основные понятия, которые будут необходимы для работы с SiriKit в приложении Xamarin.iOS. Он охватывает новый намерения и точки расширения пользовательского интерфейса Intents и как они работают с приложением и словаря пользователя, чтобы открыть приложение для Siri.

## <a name="implementing-sirikitiosplatformsirikitimplementing-sirikitmd"></a>[Реализация SiriKit](~/ios/platform/sirikit/implementing-sirikit.md)

В этой статье рассматриваются шаги, необходимые для реализации поддержки SiriKit в приложениях Xamarin.iOS. Разработчик должен руководство Основные сведения о понятиях SiriKit выше прежде чем пытаться добавить поддержку SiriKit к приложению, в качестве ключа, рассматриваются основные понятия, необходимый для успешной реализации.





## <a name="related-links"></a>Связанные ссылки

- [Пример ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Руководство по программированию в SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Ссылка на платформу намерений](https://developer.apple.com/reference/intents)
- [Ссылка на платформу пользовательского интерфейса intents](https://developer.apple.com/reference/intentsui)
