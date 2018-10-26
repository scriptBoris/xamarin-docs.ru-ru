---
title: Как задать переменные среды выполнения Mono для проектов iOS в Xamarin Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 1176CEA9-C7F1-411B-8F1A-99374E8AFF33
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/31/2017
ms.openlocfilehash: ba74e316f706e5bf22f973de4dad38d94ccd0db9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116944"
---
# <a name="how-do-i-set-mono-runtime-environment-variables-for-ios-projects-in-xamarin-studio"></a>Как задать переменные среды выполнения Mono для проектов iOS в Xamarin Studio?

Если вам нужно задать переменные среды любой среде выполнения для Mono, их можно задать **параметры проекта > выполнить > Общие** страницы.

Примечание: Переменные среды сборки мусора для SGen (MONO\_GC\_PARAMS) набора, таким образом будет использоваться только при запуске из Xamarin Studio. Если вы запустите приложение с устройства, будет игнорироваться параметры Sgen. 

Чтобы окончательно задать переменную среды для приложения, необходимо добавить в качестве аргумента дополнительных mtouch (для всех конфигураций, связанных с):

```csharp
   --setenv=NAME=VALUE
```

Переменные среды, которые могут быть установлены, можно найти по странице Mono: [ http://docs.go-mono.com/?link=man%3amono(1) ](http://docs.go-mono.com/?link=man%3amono(1)) см. в разделе: `ENVIRONMENT VARIABLES`

![](xs-mono-runtime-images/environment-variables.jpg "Задание переменных среды для проекта")
