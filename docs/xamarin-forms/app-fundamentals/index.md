---
title: Принципы работы приложения Xamarin.Forms
description: Изучение основ разработки приложений Xamarin.Forms, включая все необходимые основные понятия, с помощью для последние штрихи, например локализации и специальных возможностей.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: 515dbd2683619cfcfb7a6c8ecac6bc147265ef7d
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38995616"
---
# <a name="xamarinforms-application-fundamentals"></a>Принципы работы приложения Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Специальные возможности](accessibility/index.md)

Советы для включения возможности доступа (например, вспомогательные средства чтения с экрана) с помощью Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Класс приложения](application-class.md)

`Application` Класс является отправной точкой для Xamarin.Forms — каждое приложение необходимо реализовать подкласс `App` начальной страницы. Он также предоставляет `Properties` коллекции для хранения простых данных. Его можно определить в C# или XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Жизненный цикл приложения](app-lifecycle.md)

`Application` Класс `OnStart`, `OnSleep`, и `OnResume` методов, а также события модальной навигации, позволяют обрабатывать события жизненного цикла приложения с пользовательским кодом.

## <a name="behaviorsbehaviorsindexmd"></a>[Поведения](behaviors/index.md)

Элементы управления пользовательского интерфейса можно легко расширить без создания подклассов с помощью поведений Добавление функциональных возможностей.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Пользовательские отрисовщики](custom-renderer/index.md)

Пользовательские модули подготовки разработчиков «override» отрисовка по умолчанию элементы управления Xamarin.Forms для настройки их внешний вид и поведение на каждой платформе (с помощью соответствующих родных пакетов SDK, при необходимости).

## <a name="data-bindingdata-bindingindexmd"></a>[Привязка данных](data-binding/index.md)

Привязка данных связывает свойства двух объектов, позволяя изменять в одном свойстве автоматически отражаться в другое свойство. Привязка данных является неотъемлемой частью Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) архитектуры приложения.

## <a name="dependency-servicedependency-serviceindexmd"></a>[Службы зависимостей](dependency-service/index.md)

`DependencyService` Предоставляет простой указатель, чтобы могли кода к интерфейсам в своем общем коде и образом реализованы специфические для платформы, которые разрешаются автоматически, что упрощает для ссылки на функциональные возможности платформы в Xamarin.Forms.

## <a name="effectseffectsindexmd"></a>[Эффекты](effects/index.md)

Эффекты позволяют собственные элементы управления на каждой платформе, которую нужно изменить и обычно используются для изменения небольшой стилей.

## <a name="filesfilesmd"></a>[Файлы](files.md)

Обработка с помощью Xamarin.Forms файлов может осуществляться с помощью кода в библиотеке .NET Standard или с помощью внедренных ресурсов.

## <a name="gesturesgesturesindexmd"></a>[Жесты](gestures/index.md)

Xamarin.Forms [ `GestureRecognizer` ](xref:Xamarin.Forms.GestureRecognizer) класс поддерживает tap, сжатие и жесты панорамирования на элементы управления пользовательского интерфейса.

## <a name="localizationlocalizationindexmd"></a>[Локализация](localization/index.md)

Встроенное средство локализации .NET можно использовать для создания кросс платформенных многоязыковых приложений с помощью Xamarin.Forms.

## <a name="local-databasesdatabasesmd"></a>[Локальные базы данных](databases.md)

Xamarin.Forms поддерживает приложения на основе базы данных с помощью ядра СУБД SQLite, что делает возможным для загрузки и сохранения объектов в общем коде.

## <a name="messaging-centermessaging-centermd"></a>[Обмен сообщениями Center](messaging-center.md)

Xamarin.Forms `MessagingCenter` позволяет просматривать модели и другие компоненты для связи с без необходимости знать ничего друг о друге, помимо простого контракта сообщения.

## <a name="navigationnavigationindexmd"></a>[Навигация](navigation/index.md)

Xamarin.Forms предоставляет ряд различных способов перехода по страницам в зависимости от `Page` используемого типа.

## <a name="templatestemplatesindexmd"></a>[Шаблоны](templates/index.md)

Шаблоны элементов управления предоставляют возможность легко темы и re-theme страницы приложений во время выполнения, а шаблоны данных дают возможность настраивать представление данных в поддерживаемых элементах управления.

## <a name="triggerstriggersmd"></a>[Триггеры](triggers.md)

Обновите элементы управления, реагирование на изменения свойств и событий в XAML.


## <a name="related-links"></a>Связанные ссылки

- [Введение в Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
