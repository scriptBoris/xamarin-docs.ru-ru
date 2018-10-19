---
title: Ограничения Xamarin Live Player
description: В этом документе описываются ограничения в Xamarin Live Player. Он рассматриваются требования к устройствам, возможности он работает с типы проектов, а также другие различные темы.
ms.prod: xamarin
ms.assetid: 36A1531E-630A-4B7C-A333-4E67E5DC023C
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: aff6990df1b710190f11c2d7fa09c8399e94f8af
ms.sourcegitcommit: 79313604ed68829435cfdbb530db36794d50858f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2018
ms.locfileid: "40251024"
---
# <a name="limitations-of-xamarin-live-player"></a>Ограничения Xamarin Live Player

![Функция предварительного просмотра](~/media/shared/preview.png)

## <a name="device-requirements"></a>Требования к устройствам

Приложение Xamarin Live Player поддерживает следующим устройствам Android:

- Android 4.2 или более поздней версии.
- ARM-v7a, ARM v8a, ARM64-v8a, x 86, x86_64 процессор или.

## <a name="limitations"></a>Ограничения

Существуют некоторые ограничения на задачи, которые можно запустить Xamarin Live Player, включая перечисленные ниже:

### <a name="ios"></a>iOS

Live Player недоступна для iOS.

### <a name="xamarinforms"></a>Xamarin.Forms

- Пользовательские модули подготовки отчетов не поддерживаются.
- Эффекты не поддерживаются.
- Пользовательские элементы управления с помощью пользовательских привязываемые свойства не поддерживаются.
- Внедренные ресурсы не поддерживаются (т. е. внедрение изображений или других ресурсов в переносимую библиотеку Классов).
- Сторонних инфраструктур MVVM не поддерживаются (т. е. Prism, Mvvm Cross, Mvvm Light и т. д.).

### <a name="other-project-types"></a>Другие типы проектов

- Live Player не предназначен для собственных проектов Android (использующие Android XML для пользовательского интерфейса).

### <a name="misc"></a>Прочее

- Ограниченная поддержка для отражения (в настоящее время влияет на некоторые популярные пакеты NuGet, например SQLite и Json.NET). Возможно, другие пакеты NuGet по-прежнему поддерживается.
- Некоторые системные классы не могут быть переопределены (например, нельзя реализовать подкласс).
- Некоторые функции платформы, требующих подготовки не может работать в приложении Xamarin Live Player (тем не менее она настроена для выполнения распространенных операций, как доступ к коллекции фотографий).
- Пользовательские целевые объекты и этапы построения игнорируются. Например средств, таких как Fody, программу Refit, AutoFac и AutoMapper не может быть включен.
- Проекты F # не поддерживаются.
- Расширенные сценарии с пользовательских универсальные классы и интерфейсы могут не поддерживаться.

Используйте **сообщить о проблеме** в [Visual Studio 2017](https://docs.microsoft.com/visualstudio/ide/how-to-report-a-problem-with-visual-studio-2017) или [Visual Studio для Mac](https://docs.microsoft.com/visualstudio/mac/report-a-problem) сообщить о проблемах, связанных с Xamarin Live Player.

## <a name="related-links"></a>Связанные ссылки

- [Устранение неполадок](~/tools/live-player/troubleshooting.md)
- [Установка](~/tools/live-player/install.md)
