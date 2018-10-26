---
title: MDocArchiveToMsxDocConverter.exe не найден на сервере. BaseCommand.OnRequest
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5AC6AC4-0E7C-4746-A7CF-872F0E75AFF4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 0746174857f66843ef9a09429b6286f2efca90d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50119765"
---
# <a name="mdocarchivetomsxdocconverterexe-not-found-rverbasecommandonrequest"></a>MDocArchiveToMsxDocConverter.exe не найден на сервере. BaseCommand.OnRequest

> [!IMPORTANT]
> Эта проблема устранена в последних версиях Xamarin. Тем не менее, если эта проблема возникает на последнюю версию программного обеспечения, отправьте [новую ошибку](~/cross-platform/troubleshooting/questions/howto-file-bug.md) благодаря полное управление версиями сведения и полный создавать выходные данные журнала.


## <a name="error-message"></a>Сообщение об ошибке

Эта ошибка может возникать в *Mac Server Log* в Visual Studio:

```
Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found
 rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context, System.Object commandRequestState) [0x00000] in <filename unknown>:0
  at Mtb.Server.Listener.OnRequest (System.Object state) [0x00000] in <filename unknown>:0
```

Существует 2 отдельные проблемы в этом сообщении.

1.  `Error: /Developer/MonoTouch/usr/share/doc/MonoTouch/MDocArchiveToMsxDocConverter.exe not found`

    Эта ошибка не опасна, но он также ввести в заблуждение. Он [удаляется](https://bugzilla.xamarin.com/show_bug.cgi?id=21667) в будущем выпуске.

2.  `rver.BaseCommand.OnRequest (System.Net.HttpListenerContext context …`

    Эта ошибка является настоящую проблему. К сожалению, из-за для [ограничение](https://bugzilla.xamarin.com/show_bug.cgi?id=22080) Эта трассировка стека исключения *неполные*. Если вы заметили неполный стек трассировки следующим образом, в журнале сервера Mac, вы можете проверить `~/Library/Logs/Xamarin/MonoTouchVS/mtbserver.log` файл на узле сборки Mac, чтобы найти полная трассировка стека.
