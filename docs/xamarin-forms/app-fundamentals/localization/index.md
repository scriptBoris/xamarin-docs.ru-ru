---
title: Локализация Xamarin.Forms
description: Встроенное средство локализации .NET можно использовать для создания кросс платформенных многоязыковых приложений с помощью Xamarin.Forms. Можно локализовать текст и изображения, и приложения могут поддерживать направление потока справа налево.
ms.prod: xamarin
ms.assetid: 97BF843B-BDAA-4CEA-8189-6DB54B291D7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2018
ms.openlocfilehash: 71033e935a2d3a4be88dbcc5d975938771484640
ms.sourcegitcommit: b60a37587aad8a0bfa8a522d88d22fa672002443
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285577"
---
# <a name="xamarinforms-localization"></a>Локализация Xamarin.Forms

_Встроенное средство локализации .NET можно использовать для создания кросс платформенных многоязыковых приложений с помощью Xamarin.Forms._

## <a name="string-and-image-localizationtextmd"></a>[Локализация строк и изображений](text.md)

Встроенный механизм для локализации приложений использует .NET [файлы RESX –](https://docs.microsoft.com/dotnet/framework/resources/creating-resource-files-for-desktop-apps#resources-in-resx-files) и классы в `System.Resources` и `System.Globalization` пространства имен. Файлы RESX, содержащий переведенные строки внедряются в сборку Xamarin.Forms, а также класс, созданный компилятором, который обеспечивает строго типизированный доступ к переводы. Переведенный текст может быть извлечен в коде.

## <a name="right-to-left-localizationright-to-leftmd"></a>[Локализация справа налево](right-to-left.md)

Направление потока является направление, в котором проверяются элементы пользовательского интерфейса на странице глаза. Локализация справа налево добавлена поддержка направление потока справа налево для приложений Xamarin.Forms.
