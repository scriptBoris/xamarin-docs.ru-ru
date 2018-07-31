---
title: Понижение пакета NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 72fdf7246b148fa95ea312284957072ecda47121
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39351220"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Понижение пакета NuGet

Visual Studio для Mac, так и с помощью Visual Studio имеют функции для выбор более старых версиях пакетов и установка их автоматически. как и как работает обновление пакетов. Эти действия описаны ниже.

## <a name="visual-studio"></a>Visual Studio
1. Перейдите к **Сервис > Диспетчер пакетов NuGet > консоль диспетчера пакетов**
2. Задание проекта под **проект по умолчанию**
3. Используйте следующий синтаксис:

    > Install-Package [Имя_пакета]-версии [tab для меню версии]

Вы можете также скопировать и вставить точную команду из страницы пакета NuGet. Пример для Xamarin.Forms. [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio для Mac
1. В проекте, щелкните правой кнопкой мыши «пакеты» и выберите **добавить пакеты**
2. В searchbar чтобы найти требуемые пакеты можно использовать следующий синтаксис:

    `[PackageName] version:*`

### <a name="examples"></a>Примеры 
- Содержит список всех пакетов Xamarin.Forms: 

    `Xamarin.Forms version:`
- Содержит список всех пакетов 1.4.x Xamarin.Forms: 

    `Xamarin.Forms version:1.4`

*Примечание: Если добавить пробел между `version:` & номер версии, поиск будет вести себя как если бы, если версия не указана.*

