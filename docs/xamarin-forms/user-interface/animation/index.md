---
title: Анимация в Xamarin.Forms
description: Платформа Xamarin.Forms включает собственную инфраструктуру анимации, которая проста для создания простой анимации, отличаясь при этом достаточно универсальной для создания сложных анимаций.
ms.prod: xamarin
ms.assetid: AC0B4127-ECA3-44DA-8A24-A2B10A275083
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/14/2016
ms.openlocfilehash: bebb3e32f298a2079069787dba3453e1817cf64f
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998310"
---
# <a name="animation-in-xamarinforms"></a>Анимация в Xamarin.Forms

_Платформа Xamarin.Forms включает собственную инфраструктуру анимации, которая проста для создания простой анимации, отличаясь при этом достаточно универсальной для создания сложных анимаций._

Классы анимации Xamarin.Forms целевых разные свойства визуальных элементов, обычно анимации, постепенно изменение свойства от одного значения к другому через определенный период времени. Обратите внимание, что интерфейс не XAML для классов анимации Xamarin.Forms. Тем не менее, можно инкапсулировать анимации в [поведения](~/xamarin-forms/app-fundamentals/behaviors/index.md) , а затем ссылаться из XAML.

## <a name="simple-animationssimplemd"></a>[Простые анимации](simple.md)

[ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) Класс предоставляет методы расширения, которые могут использоваться для создания простой анимации, поворот, масштабирование, перевод и скрывать [ `VisualElement` ](xref:Xamarin.Forms.VisualElement) экземпляров. В этой статье демонстрирует создание и Отмена анимаций с помощью `ViewExtensions` класса.

## <a name="easing-functionseasingmd"></a>[Функции плавности](easing.md)

Платформа Xamarin.Forms включает [ `Easing` ](xref:Xamarin.Forms.Easing) класс, который позволяет указать функции передачи, управляющий как анимации ускорить или замедлить работу, так как они выполняются. В этой статье показано, как использовать предварительно определенных функций плавности, а также как создавать пользовательские функции плавности.

## <a name="custom-animationscustommd"></a>[Пользовательские анимации](custom.md)

[ `Animation` ](xref:Xamarin.Forms.Animation) Класс является стандартным блоком все Xamarin.Forms анимации, с помощью методов расширения в [ `ViewExtensions` ](xref:Xamarin.Forms.ViewExtensions) создание одного или нескольких классов `Animation` объектов. В этой статье демонстрируется использование `Animation` класс для создания и Отмена анимации, синхронизации нескольких анимаций и создание анимации, анимации свойств, не анимировано в существующие методы анимации.
