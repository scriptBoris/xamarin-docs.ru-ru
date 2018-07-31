---
title: Ошибка конструктора с RegisterServicePort iOS
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 929A0080-B126-4744-BF88-A4A1EFBB6CC2
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 04/03/2018
ms.openlocfilehash: 9edcc822b170c3463908b9f5fb1db8b798346e3e
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350710"
---
# <a name="ios-designer-error-with-registerserviceport"></a>Ошибка конструктора с RegisterServicePort iOS

## <a name="sample-error"></a>Пример ошибки
> System.AggregateException: Произошла одна или несколько ошибок---> System.SystemException: RegisterServicePort (com.xamarin.MTHosting.2a0b1, com.apple.PowerManagement.control): возвращается ядра: -308 (-308): умер сервера (ipc/mig)

## <a name="explanation"></a>Объяснение
Ошибки, связанные с `RegisterServicePort` и аналогичные сообщения об ошибках как выше обычно представляют собой проблему шпионского ПО и вредоносных программ на компьютере. Попробуйте [комментарий для этого отчета об ошибках](https://bugzilla.xamarin.com/show_bug.cgi?id=21907#c4) Дополнительные сведения и ссылки [обсуждения в форумах Apple](https://discussions.apple.com/thread/5596008) об удалении заражения. 

Чтобы помочь в диагностике проблемы, откройте приложение macOS **консоли** и удалите каждый файл внутри **пользователя диагностические отчеты** разделе [ http://screencast.com/t/y9i3NKcuMy ](http://screencast.com/t/y9i3NKcuMy). Затем запустите Visual Studio для Mac и попробуйте использовать конструктор. Если создание файлов журнала отображается в этом разделе после конструктора не удалось инициализировать, сохраните их для необходимости анализировать.  

Обратите внимание на то, что наиболее важно проверить, является ли этот файл: 
> /usr/lib/libimckit.dylib

Независимо от того, приведенные выше результаты если этот файл существует, упомянутых выше проблему шпионского ПО и вредоносных программ на компьютере.  

Используйте следующую ссылку включает шаги для удаления шпионских программ или вредоносной программе. [http://www.thesafemac.com/arg-genieo/](http://www.thesafemac.com/arg-genieo/)  

