---
title: Почему моя сборка выпуска Android не удается подключиться к Интернету?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A6FE770B-A19A-4BF8-95E9-2CF880D4AFC5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: cd27d5c884086cd0fade4364851039fd0cd915a0
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117915"
---
# <a name="why-cant-my-android-release-build-connect-to-the-internet"></a>Почему моя сборка выпуска Android не удается подключиться к Интернету?

## <a name="cause"></a>Причина

Наиболее распространенной причиной этой проблемы в том, что **INTERNET** разрешение автоматически включается в сборке отладки, но должен быть установлен вручную для построения выпуска. Это обусловлено разрешение Internet позволяет отладчик для присоединения к процессу, как описано для «DebugSymbols» [здесь](~/android/deploy-test/building-apps/build-process.md).


## <a name="fix"></a>Исправление

Чтобы устранить проблему, может потребоваться разрешение Internet в манифесте Android. Это можно сделать с помощью редактора манифестов или sourcecode манифеста:

-   Исправление в редактор: В проект Android, перейдите в раздел **AndroidManifest.xml на "->" Свойства "->" необходимые разрешения** и проверьте **Internet**

-   Исправление в Sourcecode: Откройте AndroidManifest в редакторе исходного кода и добавьте тег разрешение внутри `<Manifest>` теги:

    ```xml
    <Manifest>
    ...
    <uses-permission android:name="android.permission.INTERNET" />
    </Manifest>
    ```
