---
title: 'Xamarin.Essentials: Устранение неполадок'
description: В этом документе описывается устранение неполадок, возникающих при разработке с помощью библиотеки Xamarin.Essentials.
ms.assetid: 2E474FAF-F841-4E3C-B815-F7ABD8EE3361
author: jamesmontemagno
ms.author: jamont
ms.date: 06/26/2018
ms.openlocfilehash: 3dba315aec2475cb334110ba7555f773f4165aa1
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066738"
---
# <a name="xamarinessentials-troubleshooting"></a>Xamarin.Essentials: Устранение неполадок

![Предварительная версия NuGet](~/media/shared/pre-release.png)

## <a name="error-version-conflict-detected-for-xamarinandroidsupportcompat"></a>Ошибка: Для Xamarin.Android.Support.Compat обнаружен конфликт версий

Может возникнуть следующая ошибка при обновлении пакетов NuGet (или при добавлении нового пакета) с помощью Xamarin.Forms проект, использующий Xamarin.Essentials:

```
NU1107: Version conflict detected for Xamarin.Android.Support.Compat. Reference the package directly from the project to resolve this issue. 
 MyApp -> Xamarin.Essentials 0.7.0.17-preview -> Xamarin.Android.Support.CustomTabs 27.0.2 -> Xamarin.Android.Support.Compat (= 27.0.2) 
 MyApp -> Xamarin.Forms 3.1.0.583944 -> Xamarin.Android.Support.v4 25.4.0.2 -> Xamarin.Android.Support.Compat (= 25.4.0.2).
```

Проблема заключается в несоответствующие зависимости для двух NuGets. Эту ситуацию можно разрешить вручную, добавив определенной версии зависимости (в этом случае **Xamarin.Android.Support.Compat**) могут поддерживать оба.

Чтобы сделать это, добавьте NuGet, который является источником конфликта вручную и использовать **версии** список, чтобы выбрать конкретную версию. В настоящее время версия 27.0.2 Xamarin.Android.Support.Compat NuGet будет устранить эту ошибку.

Ссылаться на [этой записи блога](https://redth.codes/how-to-fix-the-dreaded-version-conflict-nuget-error-in-your-xamarin-android-projects/) Дополнительные сведения и о способах устранения проблемы.

Если возникли проблемы и ошибки сообщите нам о нем на поиск [репозитории Xamarin.Essentials GitHub](http://github.com/xamarin/Essentials).
