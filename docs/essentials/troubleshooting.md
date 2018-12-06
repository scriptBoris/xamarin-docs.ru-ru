---
title: 'Xamarin.Essentials: устранение неполадок'
description: В этом документе описывается, как устранять неполадки, возникающие при разработке с помощью библиотеки Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: 3440b572514c02c8c2240dc99b09aecfde06fcb9
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898757"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: устранение неполадок

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Ошибка: для Xamarin.Android.Support.Compat обнаружен конфликт версий

При обновлении пакетов NuGet (или добавлении нового пакета) с помощью проекта Xamarin.Forms, использующего Xamarin.Essentials, может возникнуть следующая ошибка:

```error
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.8.0-preview -> Xamarin.Android.Support.CustomTabs 27.0.2.1 -> Xamarin.Android.Support.Compat (= 27.0.2.1) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Проблема заключается в несоответствии зависимостей для двух пакетов NuGet. Это можно устранить, добавив конкретную версию зависимости вручную (в данном случае **Xamarin.Android.Support.Compat**), которая поддерживает оба пакета.

Чтобы сделать это, добавьте пакет NuGet, который является источником конфликта, вручную, выбрав конкретную версию в списке **Версия**. В настоящее время устранить эту ошибку можно с помощью пакетов NuGet Xamarin.Android.Support.Compat и Xamarin.Android.Support.Core.Util версии 27.0.2.1.

Дополнительные сведения см. в [этой записи блога](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/), содержащей видео о том, как устранить проблему.

Если у вас возникли проблемы или вы обнаружили ошибки, сообщите о них в [репозиторий Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
