---
title: API для разработчиков Xamarin.Mac macOS
description: В этом документе описывается, как считывать селекторы Objective-C и как найти соответствующие методы C# в приложении Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/02/2017
ms.openlocfilehash: 209ce2b5fb2fbb357f23e6ccc9bb849fbfedc81d
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115657"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>API для разработчиков Xamarin.Mac macOS

## <a name="overview"></a>Обзор

Большую часть времени разработки с помощью Xamarin.Mac вы думаете, читать и писать на C# не столь важно, с помощью базовых интерфейсов Objective-C API. Тем не менее иногда вы будет нужно прочитать документацию по API Apple, перевод ответ на Stack Overflow в решение для решения проблемы или сравнения в существующий образец.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>Чтение достаточно Objective-C опасными

Иногда его будет необходимо прочитать определение Objective-C, или метод вызова и переводить, эквивалентного метода C#. Давайте взглянем на определение функции Objective-C и разбить на части. Этот метод ( *селектор* в Objective-C) можно найти на `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

Объявления могут считываться слева направо:

- `-` Префикс означает, что он является методом экземпляра (нестатическим). + означает, что он является методом класса (статический)
- `(BOOL)` — Это тип возвращаемого значения (логическое значение в C#)
- `canDragRowsWithIndexes` — Это первая часть имени.
- `(NSIndexSet *)rowIndexes` — Это тип первого параметра и с ним относится к типу. Первый параметр имеет формат: `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` — Тип второго параметра и его тип. Каждый параметр, после первого имеет формат: `selectorPart:(Type) pararmName`
- — Полное имя этого сообщения селектор: `canDragRowsWithIndexes:atPoint:`. Примечание `:` в конце - очень важно.
- Фактическая привязка Xamarin.Mac C# является: `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Этот вызов селектор можно прочитать так же, как:

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- Экземпляр `v` испытывает его `canDragRowsWithIndexes:atPoint` селектора вызывается с двумя параметрами `set` и `point`, переданное в.
- В C# вызов метода выглядит следующим образом: `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Поиск члена C# для данного селектора

Теперь, когда вы нашли селекторе Objective-C, который необходимо вызвать, следующим шагом является сопоставление, эквивалентный член C#. Существует четыре подхода, можно попробовать (продолжением `NSTableView CanDragRows` пример):

1. Используйте список автоматического завершения, можно быстро находить примерно таким же именем. Так как мы знаем, что он является экземпляром класса `NSTableView` можно ввести:

    - `NSTableView x;`
    - `x.` [ctrl + пробел, если список не отображается).
    - `CanDrag` [ВВОД]
    - Щелкните правой кнопкой мыши метод, перейти к объявлению, чтобы открыть обозреватель сборок, где вы можете сравнить `Export` для рассматриваемого селектор атрибута

2. Поиск всего класса привязки. Так как мы знаем, что он является экземпляром класса `NSTableView` можно ввести:

    - `NSTableView x;`
    - Щелкните правой кнопкой мыши `NSTableView`, перейти к объявлению, чтобы обозреватель сборок
    - Поиск для рассматриваемого селектора

3. Можно использовать [электронной документации по Xamarin.Mac API](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. Мигель обеспечивает представление «Розеттском камне» API-интерфейсы Xamarin.Mac [здесь](http://tirania.org/tmp/rosetta.html) , которые можно найти через для заданного API. Если API не AppKit или конкретных macOS, может оказаться существует.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
