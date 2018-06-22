---
title: Дополнительные возможности и внутренние компоненты
description: Базовая архитектура за Xamarin.Android и его структура API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 05/21/2018
ms.openlocfilehash: 79e61db4c27a2d29b4ee0a9d39f2d25ea5d93303
ms.sourcegitcommit: 9f8e7393019791bbd6af4fefaa24a1602adabb4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2018
ms.locfileid: "34458358"
---
# <a name="advanced-concepts-and-internals"></a>Дополнительные возможности и внутренние компоненты

_Этот раздел содержит разделы с описанием архитектуры, проектирования API и ограничения Xamarin.Android. Кроме того он содержит разделы с описанием его реализация сбора мусора и сборки, которые доступны в Xamarin.Android. Так как Xamarin.Android [открытая](https://github.com/xamarin/xamarin-android), существует также возможность понять суть Xamarin.Android, изучив его исходный код._


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Архитектура](~/android/internals/architecture.md)

В этой статье описываются базовой архитектуры приложения Xamarin.Android. Он рассказывается, как приложения Xamarin.Android, выполняются в среде выполнения моно наряду с Android среды выполнения виртуальной машины и такие основные понятия, как Android вызываемых оболочек и управляемых с помощью вызываемых оболочек. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Структура API](~/android/internals/api-design.md)

Помимо основных компонентов библиотеках базовых классов, которые являются частью моно Xamarin.Android поставляется с привязок для различных Android API-интерфейсов позволяет разработчикам создавать собственные приложения Android с моно.

В основной части Xamarin.Android существует представляет механизм взаимодействия, мир мосты C# с кодом Java и предоставляет разработчикам доступ к интерфейсам API Java от C# и других языков .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Сборки](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android поставляется с несколькими сборками. Так же, как Silverlight является расширенной подмножество системной сборки .NET, Xamarin.Android также является расширенной подмножество несколько Silverlight и сборки .NET для настольных систем. 

