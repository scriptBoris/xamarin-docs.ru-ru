---
title: Почему Мой проект Xamarin.Forms.Maps Android не с НЕПРЕДВИДЕННОЙ ОШИБКОЙ верхнего уровня COMPILETODALVIK?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: C0251EB1-F509-47AD-98D6-846AF46425E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 9df9e348440b9dd4b18b3859d64cbe47bd05b24c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30790669"
---
# <a name="why-does-my-xamarinformsmaps-android-project-fail-with-compiletodalvik-unexpected-top-level-error"></a>Почему Мой проект Xamarin.Forms.Maps Android не с НЕПРЕДВИДЕННОЙ ОШИБКОЙ верхнего уровня COMPILETODALVIK?

Эта ошибка может появиться в панели ошибок Visual Studio для Mac или в окне вывода построения Visual Studio; в Android проектов, с помощью Xamarin.Forms.Maps.

Чаще всего разрешается путем увеличения размера кучи Java для проекта Xamarin.Android. Выполните следующие действия, чтобы увеличить размер кучи.

## <a name="visual-studio"></a>Visual Studio

1. Щелкните правой кнопкой мыши проект Android и откройте свойства проекта.
2. Последовательно выберите пункты **Android параметры -> Дополнительно**
3. В текстовом поле Размер кучи Java введите 1 ГБ.
4. Перестройте проект.

![Снимок экрана параметров проекта Visual Studio](maps-compiletodalvik-error-images/vsjavaheap.png "Android параметров в Visual Studio сборки")

## <a name="visual-studio-for-mac"></a>Visual Studio для Mac

1.  Щелкните правой кнопкой мыши проект Android и откройте свойства проекта.
2.  Последовательно выберите пункты **построение -> сборки Android -> Дополнительно**
3.  В текстовом поле Размер кучи Java введите 1 ГБ.
4.  Перестройте проект.  

![Снимок экрана: Visual Studio для Mac параметров проекта](maps-compiletodalvik-error-images/xsjavaheap.png "Android построения параметры в Visual Studio для Mac")

