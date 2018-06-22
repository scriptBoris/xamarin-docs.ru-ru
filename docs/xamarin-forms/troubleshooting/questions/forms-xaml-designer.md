---
title: Почему не работает в конструкторе Visual Studio XAML для Xamarin.Forms XAML-файлов
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: cab2eefb-c52f-4d81-866e-8f1feabbdd64
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 43088beba6c6a86330cac164856be98d88f07fe2
ms.sourcegitcommit: 4f646dc5c51db975b2936169547d625c78a22b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/25/2018
ms.locfileid: "34546155"
---
# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Почему не работает в конструкторе Visual Studio XAML для Xamarin.Forms XAML-файлов

Xamarin.Forms не в настоящее время поддерживает визуальные конструкторы для XAML-файлов. По этой причине при попытке открыть файл XAML формы в среде Visual Studio *конструктора пользовательского интерфейса XAML* или *пользовательского интерфейса конструктора XAML с кодировкой*, выдается следующее сообщение об ошибке:

> «Невозможно открыть файл с помощью выбранного редактора. Выберите другой редактор.»

Это ограничение описан в [Обзор](~/xamarin-forms/xaml/xaml-basics/index.md#Overview) раздел [основы XAML Xamarin.Forms](~/xamarin-forms/xaml/xaml-basics/index.md) руководства:

> «Не существует, пока визуальный конструктор для создания XAML в Xamarin.Forms приложений, поэтому вся XAML должен быть рукописный».

Тем не менее, могут отображаться средство предварительного просмотра XAML Xamarin.Forms, выбрав **представление > Другие окна > средство предварительного просмотра Xamarin.Forms** пункт меню.
