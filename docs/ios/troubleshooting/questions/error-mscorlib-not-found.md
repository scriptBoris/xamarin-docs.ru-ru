---
title: 'Ошибка времени выполнения: сборку mscorlib.dll не найдена или не удалось загрузить'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1027E16C-2C14-4BB5-AAAB-342F3E28E22E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 84bb6815c19bcacb4a9d1bddc44d340d51199c32
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105796"
---
# <a name="runtime-error-the-assembly-mscorlibdll-was-not-found-or-could-not-be-loaded"></a>Ошибка времени выполнения: сборку mscorlib.dll не найдена или не удалось загрузить

```
<Warning>: The assembly mscorlib.dll was not found or could not be loaded.
<Warning>: It should have been installed in the `/Developer/MonoTouch/Source/monotouch/builds/install/target64/lib/mono/2.0/mscorlib.dll' directory.
<Warning>: Service exited with abnormal code: 1
```

Эта проблема возникает при *скрытые* `.monotouch-32` и `.monotouch-64` папки отсутствуют `.xcarchive` для подписи или создания IPA, вызвавшая ошибку среды выполнения.

