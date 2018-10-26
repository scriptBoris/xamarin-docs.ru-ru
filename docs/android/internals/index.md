---
title: Дополнительные понятия и внутренние компоненты
description: Базовой архитектуры, лежащих в основе Xamarin.Android и ее API.
ms.prod: xamarin
ms.assetid: CC6A0D52-E9FA-4270-B3FA-84660621D6D5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/21/2018
ms.openlocfilehash: f5844dd4340afa0596219a33ed1e479a0dbcfa76
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114175"
---
# <a name="advanced-concepts-and-internals"></a>Дополнительные понятия и внутренние компоненты

_Этот раздел содержит разделы, в которых объясняется архитектура, проектирование API и ограничения для Xamarin.Android. Кроме того он включает в себя разделы, объясняющие и его реализация сбора мусора и сборок, которые доступны в Xamarin.Android. Так как Xamarin.Android [открытый](https://github.com/xamarin/xamarin-android), существует также возможность понять суть Xamarin.Android путем проверки ее исходный код._


##  <a name="architectureandroidinternalsarchitecturemd"></a>[Архитектура](~/android/internals/architecture.md)

В этой статье объясняется архитектура за приложения Xamarin.Android. Он объясняет выполнение приложений Xamarin.Android в среде выполнения Mono наряду с в среде выполнения Android виртуальной машины и объясняется такие ключевые концепции, как Android вызываемых оболочек и управляемых вызываемых оболочек. 



##  <a name="api-designandroidinternalsapi-designmd"></a>[Структура API](~/android/internals/api-design.md)

Помимо основных библиотеках базовых классов, которые являются частью Mono Xamarin.Android поставляется с привязками для различные интерфейсы API Android позволить разработчикам создавать собственные приложения Android с Mono.

В основе Xamarin.Android является подсистемой взаимодействия этим миром мосты C# со всем миром Java и предоставляет разработчикам доступ к API-интерфейсы Java в C# и других языков .NET.



##  <a name="assembliescross-platforminternalsavailable-assembliesmd"></a>[Сборки](~/cross-platform/internals/available-assemblies.md)

Xamarin.Android поставляется с несколькими сборками. Так же, как Silverlight является расширенной подмножество классические сборки .NET, Xamarin.Android также является расширенной подмножеством несколько Silverlight и классические сборки .NET. 

