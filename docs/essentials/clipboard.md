---
title: 'Xamarin.Essentials: Clipboard'
description: В этом документе описывается класс Clipboard в Xamarin.Essentials, который позволяет копировать текст в системный буфер обмена и вставлять его между приложениями.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 8dd238da678dfb5773801137d313b286590aa463
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675540"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: Clipboard

![Предварительная версия NuGet](~/media/shared/pre-release.png)

Класс **Clipboard** позволяет копировать текст в системный буфер обмена и вставлять его между приложениями.

## <a name="get-started"></a>Начало работы

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-clipboard"></a>Использование Clipboard

Добавьте в свой класс ссылку на Xamarin.Essentials:

```csharp
using Xamarin.Essentials;
```

Чтобы проверить, есть ли в классе **Clipboard** текст, готовый для вставки, используйте следующий код:

```csharp
var hasText = Clipboard.HasText;
```

Чтобы поместить текст в класс **Clipboard**, используйте следующий код:

```csharp
Clipboard.SetText("Hello World");
```

Чтобы прочесть текст из класса **Clipboard**, используйте следующий код:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Исходный код Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Документация по API Clipboard](xref:Xamarin.Essentials.Clipboard)
