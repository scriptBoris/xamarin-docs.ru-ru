---
title: Привязка библиотек iOS
description: В этом документе описывается создание привязки C# для кода Objective-C, что позволяет использовать собственные библиотеки и CocoaPods в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: EBDC50DC-B44B-4003-AB2B-1EEB868A5E01
ms.technology: xamarin-ios
ms.custom: xamu-video
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/18/2017
ms.openlocfilehash: 75c8f9a2eea080c3da031366b314d94929080811
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855225"
---
# <a name="binding-ios-libraries"></a>Привязка библиотек iOS

Используйте следующие ссылки, чтобы узнать о привязке библиотеки Objective-C и CocoaPods для Xamarin.iOS и Xamarin.Mac.

- [**Общие сведения о** ](~/cross-platform/macios/binding/overview.md) -
  описан принцип действия привязки.
- [**Привязка библиотек Objective-C** ](~/cross-platform/macios/binding/objective-c-libraries.md) -
  инструкции о том, как выполнить привязку библиотеки Objective-C для использования в проектах Xamarin.
- [**Введите справочное руководство по определению** ](~/cross-platform/macios/binding/binding-types-reference.md) -
  описывает все атрибуты, доступные для авторов привязки для управления процессом создания привязки.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Цели Sharpie — это средство командной строки для начальной загрузки при первом проходе привязки.
Он работает путем анализа файлов заголовков из собственной библиотеки для сопоставления открытого API-интерфейса в [определения привязки](~/cross-platform/macios/binding/objective-c-libraries.md) (этот процесс, в противном случае выполняется вручную). Цели Sharpie не обеспечивает привязку, сам по себе, но он может помочь приступить к работе!

Цели Sharpie 3.0 появилась возможность напрямую привязать Cocoapods!

## <a name="walkthrough---binding-an-ios-objective-c-librarywalkthroughmd"></a>[Пошаговое руководство. Привязка библиотек Objective-C iOS](walkthrough.md)

Эта страница содержит пошаговое руководство по созданию проекта привязки iOS с помощью с открытым исходным кодом [ **InfColorPicker** ](https://github.com/InfinitApps/InfColorPicker) проекта Objective-C в качестве примера. **InfColorPicker** библиотека предоставляет контроллер многократно используемых представления, которые позволяют пользователю выбирать цвета в его представление HSB, выбора цвета более понятные.
Цели Sharpie будет использоваться для помощи в процессе привязки.

## <a name="xamarin-university-lightning-lecture"></a>Лекции молнии Xamarin University

> [!VIDEO https://youtube.com/embed/ZUoPLcmnf1o]

**iOS привязками в C/C++, с [Xamarin University](https://university.xamarin.com/)**

## <a name="related-links"></a>Связанные ссылки

- [Привязка Objective-C](~/cross-platform/macios/binding/index.md)
- [Привязка Mac](~/mac/platform/binding.md)
- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)