---
title: Где хранятся компоненты на моем компьютере?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5EBB49EE-39E5-428B-866F-9FC1BB215B31
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 4152c8ef7eeba3748d9244e27e48f3f9a2c0019b
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350723"
---
# <a name="where-are-the-components-stored-on-my-machine"></a>Где хранятся компоненты на моем компьютере?

При установке компонента Xamarin в проекте приложения, он помещается в двух местах:

1. В папке компоненты на корневом уровне папки решения. При удалении компонента из всех проектов в решении, он будут удалены из этой папки.

2. Копии также хранятся в следующих расположениях:
    - Windows: `%LocalAppData%\Xamarin\Cache\Components`
    - MAC: `~/Library/Caches/Xamarin/Components`

Поэтому чтобы полностью удалить компонент из системы, удалите его из проектов и решений, а также из папки кэша выше.
