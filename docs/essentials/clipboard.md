---
title: 'Xamarin.Essentials: буфер обмена'
description: В этом документе описывается класс Clipboard в Xamarin.Essentials, который позволяет копировать текст в системный буфер обмена и вставлять его между приложениями.
ms.assetid: C52AE99A-0FB3-425D-9106-3DA5777FEFA0
author: jamesmontemagno
ms.author: jamont
ms.date: 02/12/2019
ms.custom: video
ms.openlocfilehash: 3511850391b2be809daf2b70e81fa5b591db8dfa
ms.sourcegitcommit: c6ff24b524d025d7e87b7b9c25f04c740dd93497
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56240348"
---
# <a name="xamarinessentials-clipboard"></a>Xamarin.Essentials: буфер обмена

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
await Clipboard.SetTextAsync("Hello World");
```

Чтобы прочесть текст из класса **Clipboard**, используйте следующий код:

```csharp
var text = await Clipboard.GetTextAsync();
```

## <a name="api"></a>API

- [Исходный код Clipboard](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Clipboard)
- [Документация по API Clipboard](xref:Xamarin.Essentials.Clipboard)

## <a name="related-video"></a>Связанные видео

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Clipboard-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
