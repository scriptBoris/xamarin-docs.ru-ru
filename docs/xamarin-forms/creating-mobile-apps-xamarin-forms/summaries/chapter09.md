---
title: Сводка Глава 9. Вызовы API конкретных платформ
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 9. Вызовы API конкретных платформ'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 8a035da3dec468df291a19849ca89964c6707589
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994761"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Сводка Глава 9. Вызовы API конкретных платформ

Иногда бывает необходимо выполнять определенный код, которая зависит от платформы. В этой главе рассматриваются методы.

## <a name="preprocessing-in-the-shared-asset-project"></a>Предварительная обработка в проект общего ресурса

Проект Xamarin.Forms общего ресурса можно выполнить разный код для каждой платформы, с помощью директивы препроцессора C# `#if`, `#elif`, и `endif`. Это показано в [ **PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Тройной снимок экрана: переменная в формате абзаца](images/ch09fg01-small.png "модель устройства и операционной системы")](images/ch09fg01-large.png#lightbox "модель устройства и операционной системы")

Тем не менее результирующий код может быть некрасиво и трудно читать.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Параллельные классы в проекте общего ресурса

Демонстрируется более структурированный подход к выполнению кода для конкретных платформ в SAP в [ **PlatInfoSap2** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) образца. Каждый из проектов платформа содержит класс с одинаковыми именами и методы, но реализован для данной конкретной платформы. SAP просто создает экземпляр класса и вызывает метод.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService и переносимой библиотеки классов

Библиотеки не может получить доступ к обычно классов в проекты приложений. Кажется, что это ограничение для предотвращения метод, описанный в **PlatInfoSap2** больше не использовался в PCL. Тем не менее, Xamarin.Forms содержит класс с именем [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) , использует отражение .NET для доступа к открытых классов в проекте приложения из библиотеки PCL.

Переносимая библиотека Классов необходимо определить `interface` с элементами, которые нужны для работы каждой из платформ. Затем в каждой из платформ содержит реализацию этого интерфейса. Класс, реализующий интерфейс должен быть идентифицирован по [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) на уровне сборки.

Переносимая библиотека Классов, затем использует универсальный [ `Get` ](xref:Xamarin.Forms.DependencyService.Get*) метод `DependencyService` для получения экземпляра класса платформы, который реализует интерфейс.

Это показано в [ **DisplayPlatformInfo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) образца.

## <a name="platform-specific-sound-generation"></a>Генерация звука платформы

[ **MonkeyTapWithSound** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) пример добавляет звуковых сигналов для **MonkeyTap** программы, обратившись к звуковому средства каждой из платформ.



## <a name="related-links"></a>Связанные ссылки

- [Глава 9 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Глава 9-примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Службы зависимостей](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
