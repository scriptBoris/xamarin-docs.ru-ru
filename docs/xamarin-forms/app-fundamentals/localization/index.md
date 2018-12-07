---
title: Локализация Xamarin.Forms
description: Встроенную платформу локализации .NET можно использовать в кроссплатформенных многоязыковых приложениях в Xamarin.Forms. Можно локализовать текст и изображения, а приложения могут поддерживать направление потока справа налево.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285577"
---
# <a name="xamarinforms-localization"></a>Локализация Xamarin.Forms

_Встроенную платформу локализации .NET можно использовать в кроссплатформенных многоязыковых приложениях в Xamarin.Forms._

## <a name="string-and-image-localizationtextmd"></a>[Локализация строк и изображений](text.md)

Встроенный механизм для локализации приложений использует [RESX-файлы](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) и классы в пространствах имен `System.Resources` и `System.Globalization`. RESX-файлы, содержащие переведенные строки, внедряются в сборку Xamarin.Forms вместе с создаваемым компилятором классом, который обеспечивает строго типизированный доступ к переводам. Затем переведенный текст можно извлечь в коде.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Локализация справа налево](right-to-left.md)

Направление потока — это направление, в котором глаз человека перемещается по элементам пользовательского интерфейса на странице. Локализация справа налево поддерживает направление потока справа налево в приложениях Xamarin.Forms.
