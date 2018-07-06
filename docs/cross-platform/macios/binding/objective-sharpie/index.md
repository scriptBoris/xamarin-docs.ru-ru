---
title: Создание привязки с помощью цели Sharpie
description: В этом разделе содержатся вводные Sharpie цели, Xamarin средство командной строки, используемые для автоматизации процесса создания привязки в библиотеку Objective-C
ms.prod: xamarin
ms.assetid: 9C0A932C-7601-4357-B3F7-62ABAC835019
author: asb3993
ms.author: amburns
ms.date: 10/11/2017
ms.openlocfilehash: 53fcbbc408ae147405a3285d9391457051d6e16e
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854802"
---
# <a name="creating-bindings-with-objective-sharpie"></a>Создание привязки с помощью цели Sharpie

_В этом разделе содержатся вводные Sharpie цели, Xamarin средство командной строки, используемые для автоматизации процесса создания привязки в библиотеку Objective-C_

- [Общие сведения о](#overview) & [журнал](#history)
- [Начало работы](get-started.md)
- [Средства и команды](tools.md)
- [Функции](platform/index.md)
- [Примеры](examples/index.md)
- [Полное Пошаговое руководство](~/ios/platform/binding-objective-c/walkthrough.md)
- [История выпусков](releases.md)

## <a name="overview"></a>Обзор

Цели Sharpie — это средство командной строки для начальной загрузки при первом проходе привязки.
Он работает путем анализа файлов заголовков из собственной библиотеки для сопоставления открытого API-интерфейса в [определения привязки](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) (этот процесс, который ранее вручную было сделано).

Цели Sharpie использует Clang анализировать файлы заголовка, поэтому привязка осуществляется точно и подробный. Это может значительно снизить время и усилия, требуемые для создания привязки качества.

> [!IMPORTANT]
> Цели Sharpie — это средство для опытных разработчиков Xamarin с помощью дополнительных знаний Objective-c (а следовательно, C). Прежде чем пытаться привязать библиотеку Objective-C необходимо четко понимать способы создания собственной библиотеки в командной строке (и хорошего понимания принципов работы собственной библиотеки).

## <a name="history"></a>Журнал

Мы были развивается и использования Sharpie цели в Xamarin за последние три года. Благодаря возможности Sharpie цели API-интерфейсы введенные в Xamarin.iOS и Xamarin.Mac после выхода версии iOS 8, Mac OS X 10.10 и watchOS 2.0 были начальную загрузку с помощью Sharpie цели. Xamarin основывается на цели Sharpie внутренним образом для создания собственных продуктов.

Однако цель Sharpie является очень сложных средство, которое требует дополнительных знаний о Objective-C и C, как использовать в командной строке компилятора clang и собрали обычно как собственные библиотеки. Из-за этого высокую планку, мы полагаем, что с графическим интерфейсом пользователя мастер устанавливает неправильный ожидания, и таким образом, цели Sharpie в данный момент доступна только как средство командной строки.

## <a name="related-links"></a>Связанные ссылки

- [Цели Sharpie загрузки](https://dl.xamarin.com/objective-sharpie/ObjectiveSharpie.pkg)
- [Пошаговое руководство: Привязка библиотек Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)
- [Привязка библиотек Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Сведения о привязке](~/cross-platform/macios/binding/overview.md)
- [Привязки типов справочное руководство](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin для разработчиков Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
