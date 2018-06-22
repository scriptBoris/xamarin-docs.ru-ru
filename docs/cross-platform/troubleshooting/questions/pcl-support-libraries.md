---
title: Как можно ли узнать, какие библиотеки поддерживаются в PCL?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
author: asb3993
ms.author: amburns
ms.openlocfilehash: 87f65ba2cff2d5990c32aa142f97766a76d6ba05
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33919544"
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>Как можно ли узнать, какие библиотеки поддерживаются в PCL?

- Можно найти обзор различных возможностей, поддерживаемых различными PCL целевых платформ в разделе *поддерживаемые функции* части этой страницы: [http://msdn.microsoft.com/library/gg597391.aspx](https://msdn.microsoft.com/library/gg597391.aspx)

- Другой вариант — использовать [анализатор переносимости .NET](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) для определения соответствия можно преобразовать существующие библиотеки PCL профиля.

- Третий возможностью является осуществляете фактическое профиль, который можно использовать. Например, используя профиль 78, может открыться здесь: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` и просмотреть все сборки в ней.

Какой бы метод вы выбрали, пожалуйста Обратите внимание, что некоторые функции должен быть загружен с помощью NuGet и библиотеки Microsoft BCL.
