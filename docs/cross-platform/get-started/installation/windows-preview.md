---
title: Установка предварительной версии Xamarin в ОС Windows
description: Этот документ описывает установку предварительной версии Xamarin в Visual Studio 2017 с помощью канала предварительного выпуска.
ms.prod: xamarin
ms.assetid: 9F730444-06E8-4B3F-8A19-CA95CD484FFA
author: asb3993
ms.author: amburns
ms.date: 03/20/2018
ms.openlocfilehash: d920dd688a7911ccf4002d67914c977da56f89e1
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780689"
---
# <a name="installing-xamarin-preview-on-windows"></a>Установка предварительной версии Xamarin в ОС Windows

Visual Studio 2017 не поддерживает альфа- и бета-каналы и стабильные каналы так, как предыдущие версии. Вместо этого есть два варианта:

- **выпуск** — эквивалент _стабильного_ канала в Visual Studio для Mac;
- **предварительная версия** — эквивалент _альфа-_ и _бета-_ каналов в Visual Studio для Mac.

> [!TIP] 
> Для пробного использования функций предварительной версии следует [скачать установщик предварительной версии Visual Studio 2017](https://www.visualstudio.com/vs/preview/), который предложит установить **предварительные версии** Visual Studio параллельно со стабильной версией (версией выпуска). Дополнительные сведения о новых возможностях Visual Studio 2017 можно найти в [заметках о выпуске](/visualstudio/releasenotes/vs2017-preview-relnotes).

Предварительная версия Visual Studio может включать соответствующие предварительные версии функциональных возможностей Xamarin, в том числе следующие:

- Xamarin.Forms
- Xamarin.iOS
- Xamarin.Android
- Xamarin Profiler
- Xamarin Workbooks или Inspector
- Симулятор iOS удаленной работы для Xamarin

На снимке экрана **установщика предварительной версии** ниже показаны параметры и предварительной версии, и выпуска (обратите внимание на номера версий, указанные серым цветом: версия 15.0 относится к выпуску, а версия 15.1 — к предварительной версии):

![отображение параметров предварительной версии в установщике](windows-images/vs2017-installer.jpg)

В процессе установки, если выполняется параллельная установка, может применяться **псевдоним установки** (чтобы версии можно было различить в меню "Пуск"), как показано ниже:

[![Изменение псевдонима перед установкой](windows-images/vs2017-nickname-sml.png "edit nickname before installing")](windows-images/vs2017-nickname.png#lightbox)

### <a name="uninstalling-visual-studio-2017-preview"></a>Удаление предварительной версии Visual Studio 2017

**Установщик Visual Studio** также следует использовать для удаления установленных предварительных версий Visual Studio 2017. Дополнительные сведения см. в руководстве по [удалению Xamarin](uninstalling-xamarin.md#uninstallvs2017).
