---
title: Ошибка конструктора с RegisterServicePort iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 62d4a06c62bffb23566f4f59f42a0c980f417c45
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776720"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Ошибка конструктора с RegisterServicePort iOS

## <a name="sample-error"></a>Пример ошибки
> System.AggregateException: Произошла одна или несколько ошибок---> System.SystemException: RegisterServicePort (com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): возвращаемые ядра: -308 (-308): (ipc и mig) server прервано

## <a name="explanation"></a>Объяснение
Ошибки, связанные с `RegisterServicePort` и аналогичные сообщения об ошибках как выше обычно являются проблемы с шпионского ПО или вредоносных программ на компьютере. Попробуйте [комментарий в этот отчет об ошибке](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) Дополнительные сведения и ссылки [Apple форум обсуждений](https://discussions.apple.com/thread/5596008) по удалению заражения. 

Для диагностики проблемы, откройте приложение macOS **консоли** и удалите каждый файл внутри **диагностические отчеты пользователя** раздел [ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy). Затем запустите Visual Studio для Mac и попытаться использовать конструктор. Если создание файлов журнала отображается в этом разделе после конструктора не удалось инициализировать, сохраните их для нас для анализа.  

Обратите внимание, что этот файл является наиболее важным для проверки: 
> /usr/lib/libimckit.dylib

Независимо от того, приведенные выше результаты, если этот файл существует, упомянутых выше шпионского ПО или вредоносных программ проблема заключается в установлена на компьютере.  

По следующей ссылке включает шаги для удаления программ-шпионов и вредоносные программы. [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

