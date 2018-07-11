---
title: 'Xamarin.Essentials: Устранение неполадок'
description: В этом документе описывается, как устранять неполадки, возникающие при разработке с помощью библиотеки Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 8cb18ab029d2fd161c60fda7e130f319b8f0c3ab
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947378"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Устранение неполадок

![Предварительные версии NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Ошибка: Для Xamarin.Android.Support.Compat обнаружен конфликт версий

Может возникнуть следующая ошибка при обновлении пакетов NuGet (или Добавление нового пакета) с помощью Xamarin.Forms проект, использующий Xamarin.Essentials:

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Проблема заключается в несоответствие зависимости для двух пакеты NuGet. Это можно устранить, вручную добавив определенной версии зависимости (в данном случае **Xamarin.Android.Support.Compat**), может поддерживать оба.

Чтобы сделать это, добавьте NuGet, который является источником конфликта вручную и используйте **версии** списка, чтобы выбрать определенную версию. В настоящее время версии 27.0.2.1 Xamarin.Android.Support.Compat и Xamarin.Android.Support.Core.Util NuGet будет устранить эту ошибку.

Ссылаться на [этой записи блога](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) Дополнительные сведения и видео о том, как устранить проблему.

Если возникли проблемы или ошибки отправьте сообщение на поиск [репозиторий Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
