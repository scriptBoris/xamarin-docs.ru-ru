---
title: Привязка Objective-C
description: Этот документ содержит ссылки на различные руководства, описывающие способы создания привязки C# для кода Objective-C, что позволяет разработчикам использовать готовые библиотеки в приложениях Xamarin.
ms.prod: xamarin
ms.assetid: DBBAA086-BB0F-8161-DF44-632F4F5DFE5D
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: 3f1e1ce324e849c0c939d936eb6ee1470cf24a3b
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855160"
---
# <a name="binding-objective-c"></a>Привязка Objective-C

Этот раздел содержит широкий набор документов, которые покрывают Создание привязок для библиотеки Objective-C, поэтому они могут вызываться из приложения C#, созданные с помощью Xamarin.iOS и Xamarin.Mac.

##  <a name="overviewcross-platformmaciosbindingoverviewmd"></a>[Обзор набора средств Visual Studio для Unity](~/cross-platform/macios/binding/overview.md)

Этот документ содержит некоторые из внутренних как выполняется привязка. Он представляет собой расширенный документ некоторые технические сведения.

##  <a name="binding-objective-c-librariescross-platformmaciosbindingobjective-c-librariesmd"></a>[Привязка библиотек Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)

В этом документе описывается процесс, используемый для создания привязки Objective-C API и как стили в Objective-C сопоставляются идиомы, используемые в .NET C#.
При связывании просто C API-интерфейсы, следует использовать стандартный механизм .NET для этого платформа P/Invoke.

##  <a name="binding-definition-reference-guidecross-platformmaciosbindingbinding-types-referencemd"></a>[Справочное руководство для определения привязки](~/cross-platform/macios/binding/binding-types-reference.md)

Это руководство, описывающее все атрибуты, доступные для авторов привязки для управления процессом создания привязки.


## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Цели Sharpie — это средство командной строки для начальной загрузки при первом проходе привязки. Он работает путем анализа файлов заголовков из собственной библиотеки для сопоставления открытого API-интерфейса в [определения привязки](~/cross-platform/macios/binding/objective-c-libraries.md) (этот процесс, можно также вручную).

## <a name="ios"></a>iOS

[Страницу привязки iOS](~/ios/platform/binding-objective-c/index.md) ссылается на общие ресурсы привязки, в дополнение к в приведенных ниже примерах.

### <a name="walkthrough-binding-an-objective-c-libraryiosplatformbinding-objective-cwalkthroughmd"></a>[Пошаговое руководство: Привязка библиотек Objective-C](~/ios/platform/binding-objective-c/walkthrough.md)

Статья содержит пошаговое руководство по созданию проекта привязки с помощью с открытым исходным кодом [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) проекта Objective-C в качестве примера. Библиотека InfColorPicker предоставляет контроллер многократно используемых представления, которые позволяют пользователю выбирать цвета в его представление HSB, выбора цвета более понятные. Цели Sharpie будет использоваться для помощи в процессе привязки.

### <a name="binding-sampleshttpsgithubcommonomonotouch-bindings"></a>[Примеры привязок](https://github.com/mono/monotouch-bindings)

Коллекцию привязок независимых производителей, которые можно использовать ссылку, при создании новых проектов привязки.

## <a name="mac"></a>Mac

Исторически [привязки Mac](~/mac/platform/binding.md) была очень вручную. Сейчас [загружаемые preview](https://forums.xamarin.com/discussion/59760/xamarin-mac-binding-project-preview) поддержки проект привязки Mac в будущих выпусках Visual Studio для Mac.



## <a name="related-links"></a>Связанные ссылки

- [iOS привязки](~/ios/platform/binding-objective-c/index.md)
- [Привязка Mac](~/mac/platform/binding.md)
- [Xamarin University курс: Создание библиотеку привязки Objective-C](https://university.xamarin.com/classes/track/all#building-an-objective-c-bindings-library)
- [Xamarin University курс: Создание библиотеки привязки Objective-C с помощью цели Sharpie](https://university.xamarin.com/classes/track/all#build-an-objective-c-bindings-library-with-objective-sharpie)
