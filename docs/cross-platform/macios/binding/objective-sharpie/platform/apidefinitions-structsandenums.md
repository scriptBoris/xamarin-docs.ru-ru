---
title: ApiDefinitions и StructsAndEnums файлов
description: В этом документе описаны ApiDefinitions.cs и StructsAndEnums.cs файлы, которые создает Sharpie цели. Затем эти файлы используются для доступа к коду Objective-C из C#.
ms.prod: xamarin
ms.assetid: AC2087C0-BA54-46D8-B70C-6972941C8F73
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: df8d4508db14116a5b36e893f161ac891d58dc46
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855186"
---
# <a name="apidefinitions--structsandenums-files"></a>ApiDefinitions и StructsAndEnums файлов

Когда цель Sharpie выполнится успешно, он создает `Binding/ApiDefinitions.cs` и `Binding/StructsAndEnums.cs` файлы.
Эти файлы добавляются в проект привязки в Visual Studio для Mac или переданные непосредственно в директиву `btouch` или `bmac` средства для создания окончательного привязки.

В *некоторые* случаи, созданные файлы может быть все, что нужно, однако чаще разработчику потребуется вручную изменять эти созданные файлы для устранения проблем, которые не удалось обработать автоматически с помощью средства (например, эти помеченные с помощью [ `Verify` атрибут](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md)).

Ниже перечислены некоторые из следующих шагов.

- **Настройка имен**: иногда требуется настроить имена методов и классов в соответствии с рекомендации по разработке .NET Framework.
- **Методы или свойства**: эвристические правила, используемые Sharpie цели иногда выбирает метод для включения в свойство. На этом этапе вы решите, является ли это предполагаемое поведение, или нет.
- **Обработка событий**: можно связать классов с классами в делегат и автоматически создавать события для тех.
- **Подключить уведомления**: невозможно для извлечения контракта API-интерфейса уведомлений из файлов чисто заголовков, для этого потребуется поездку на документацию по API. Если требуется строго типизированных уведомлений, необходимо будет обновить результат.
- **API проверки**: на этом этапе вы можете предоставить дополнительные конструкторы, добавьте методы (чтобы допускать синтаксис инициализации на построение C#), перегрузки операторов и реализовать собственные интерфейсы на файле дополнительных определений.

См. в разделе [привязки API](~/cross-platform/macios/binding/objective-c-libraries.md) описание, чтобы увидеть, как эти файлы помещаются в процесса привязки, как показано на следующей схеме:

![](apidefinitions-structsandenums-images/binding-flowchart.png "На этой схеме показан процесс привязки")

Ссылаться на [Справочник по типам привязки](~/cross-platform/macios/binding/binding-types-reference.md) Дополнительные сведения о содержимое этих файлов.

## <a name="related-links"></a>Связанные ссылки

- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
