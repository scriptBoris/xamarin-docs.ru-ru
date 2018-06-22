---
title: Где хранятся компоненты на моем компьютере?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.openlocfilehash: a53045a6179a26b30d824976d11fd2769a84811e
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919401"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Где хранятся компоненты на моем компьютере?

При установке компонента Xamarin в проекте приложения, он помещается в двух местах:

1. В папке компоненты на корневом уровне папкой решения. При удалении компонента из всех проектов в решении, он будет удаляются из этой папки, а также.

2. Копии хранятся также в следующих расположениях:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - MAC: `~/Library/Caches/Xamarin/Components`

Поэтому чтобы полностью удалить компонент из системы, удалите из проектов и решений и выше папки кэша.
