---
title: Принципы работы приложения Xamarin.Forms
description: Изучение основ разработки приложений Xamarin.Forms, включая все необходимые основные понятия, через, чтобы внести Завершающие штрихи, например доступность и локализации.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/28/2017
ms.openlocfilehash: d75cac7a21b2c74a6627845cdda8e4c04e72bddc
ms.sourcegitcommit: eac092f84b603958c761df305f015ff84e0fad44
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309779"
---
# <a name="xamarinforms-application-fundamentals"></a>Принципы работы приложения Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Специальные возможности](accessibility/index.md)

Советы для включения возможности доступа (например, при поддержке средств чтения с экрана) с помощью Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Класс приложения](application-class.md)

`Application` Класса является отправной точкой для Xamarin.Forms — каждого приложения, должен реализовывать подкласс `App` начальной страницы. Он также предоставляет `Properties` коллекцию для хранения данных. Он может быть определен в C# или XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Жизненный цикл приложения](app-lifecycle.md)

`Application` Класса `OnStart`, `OnSleep`, и `OnResume` методов, а также события модальное навигации позволяют обрабатывать события жизненного цикла приложения с пользовательским кодом.

## <a name="behaviorsbehaviorsindexmd"></a>[Поведения](behaviors/index.md)

Элементы управления пользовательского интерфейса можно легко расширить, без создания подкласса с помощью поведений для расширения функциональности.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Пользовательские отрисовщики](custom-renderer/index.md)

Пользовательские модули подготовки позволяют разработчикам переопределять отрисовки по умолчанию элементов управления Xamarin.Forms и настроить их внешний вид и поведение для каждой платформы (с помощью собственного пакетов SDK, при необходимости).

## <a name="data-bindingdata-bindingindexmd"></a>[Привязка данных](data-binding/index.md)

Привязка данных связывает свойства двух объектов, позволяя изменять в одном свойстве, автоматически отобразятся в других свойств. Привязка данных является неотъемлемой частью Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)) архитектуры приложения.

## <a name="dependency-servicedependency-serviceindexmd"></a>[Службы зависимостей](dependency-service/index.md)

`DependencyService` Предоставляет простой указателя, можно в общем коде в код интерфейсы и предоставления реализаций специфический для платформы, которые разрешаются автоматически, облегчая ссылаются на определенную платформу функциональные возможности в Xamarin.Forms.

## <a name="effectseffectsindexmd"></a>[Эффекты](effects/index.md)

Эффекты позволяют собственные элементы управления на каждой платформе, которую нужно изменить и обычно используются для изменения небольшой стилей.

## <a name="filesfilesmd"></a>[Файлы](files.md)

Обработка с помощью Xamarin.Forms файлов может осуществляться с помощью кода в библиотеке .NET Standard или с помощью внедренных ресурсов.

## <a name="gesturesgesturesindexmd"></a>[Жесты](gestures/index.md)

Xamarin.Forms [ `GestureRecognizer` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GestureRecognizer/) класс поддерживает tap, сжатие и сдвиг жестов на элементы управления пользовательского интерфейса.

## <a name="localizationlocalizationindexmd"></a>[Локализация](localization/index.md)

Встроенные локализации .NET framework можно использовать для создания разных платформах многоязыковых приложений с помощью Xamarin.Forms.

## <a name="local-databasesdatabasesmd"></a>[Локальные базы данных](databases.md)

Xamarin.Forms поддерживает приложения на основе базы данных, используя компонент database engine SQLite, что делает возможным для загрузки и сохранения объектов в общем коде.

## <a name="messaging-centermessaging-centermd"></a>[Center обмена сообщениями](messaging-center.md)

Xamarin.Forms `MessagingCenter` позволяет просматривать модели и другие компоненты для взаимодействия с даже не зная ничего о друг с другом помимо простого контракта сообщения.

## <a name="navigationnavigationindexmd"></a>[Навигация](navigation/index.md)

Xamarin.Forms предоставляет ряд возможностей навигации другую страницу, в зависимости от того `Page` типа.

## <a name="templatestemplatesindexmd"></a>[Шаблоны](templates/index.md)

Шаблоны элементов управления предоставляют возможность легко темы и re темы страницы приложения во время выполнения, шаблоны данных предоставляют возможность определения представления данных в элементе управления, поддерживаемых.

## <a name="triggerstriggersmd"></a>[Триггеры](triggers.md)

Обновите элементы управления, реагирование на изменения свойств и событий в XAML.


## <a name="related-links"></a>Связанные ссылки

- [Введение в Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
