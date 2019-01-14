---
title: Основы разработки приложения Xamarin.Forms
description: Изучение основ разработки приложения Xamarin.Forms, включая все необходимые концепции, вплоть до специальных возможностей и локализации.
ms.prod: xamarin
ms.assetid: 7B516BBC-F7E1-4387-9779-7754E2E69723
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: a65946f21f8ced00e9ad64aec590df37acab1528
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207821"
---
# <a name="xamarinforms-application-fundamentals"></a>Основы разработки приложения Xamarin.Forms

## <a name="accessibilityaccessibilityindexmd"></a>[Специальные возможности](accessibility/index.md)

Советы по внедрению функций специальных возможностей (таких как средства чтения с экрана) в Xamarin.Forms.

## <a name="app-classapplication-classmd"></a>[Класс приложения](application-class.md)

Класс `Application` является отправной точкой для Xamarin.Forms — каждому приложению нужно реализовать вложенный класс `App`, чтобы задать начальную страницу. Он также предоставляет коллекцию `Properties` для простого хранилища данных. Его можно задать через C# или XAML.

## <a name="app-lifecycleapp-lifecyclemd"></a>[Жизненный цикл приложения](app-lifecycle.md)

Методы `OnStart`, `OnSleep` и `OnResume` класса `Application`, а также события модальной навигации позволяют обрабатывать события жизненного цикла приложений с помощью пользовательского кода.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Индексирование приложения и создание глубинных ссылок](deep-linking.md)

Индексирование приложения позволяет приложениям, которые в противном случае забываются после нескольких использований, оставаться релевантными благодаря отображению в результатах поиска. Создание прямых ссылок позволяет приложениям реагировать на результат поиска, который содержит данные приложения, как правило путем перехода на страницу, на которую ссылается прямая ссылка.

## <a name="behaviorsbehaviorsindexmd"></a>[Поведения](behaviors/index.md)

Элементы управления пользовательского интерфейса можно легко расширить без вложенных классов с помощью добавления функций.

## <a name="custom-rendererscustom-rendererindexmd"></a>[Пользовательские отрисовщики](custom-renderer/index.md)

Пользовательские отрисовщики позволяют разработчикам переопределить отрисовку элементов управления Xamarin.Forms по умолчанию, чтобы настроить их внешний вид и поведение на каждой платформе (при необходимости используйте собственные пакеты SDK).

## <a name="data-bindingdata-bindingindexmd"></a>[Привязка данных](data-binding/index.md)

Привязка данных связывает свойства двух объектов так, чтобы изменения в одном свойстве автоматически отражались в другом. Привязка данных — неотъемлемая часть архитектуры приложения Model-View-ViewModel ([MVVM](~/xamarin-forms/enterprise-application-patterns/mvvm.md)).

## <a name="dependency-servicedependency-serviceindexmd"></a>[Служба зависимостей](dependency-service/index.md)

`DependencyService` предоставляет простой указатель, позволяющий кодировать интерфейсы в общем коде и предоставлять автоматически разрешающиеся реализации платформы. С их помощью можно легко ссылаться на функции платформы в Xamarin.Forms.

## <a name="effectseffectsindexmd"></a>[Эффекты](effects/index.md)

Эффекты позволяют настраивать собственные элементы управления на каждой платформе. Они обычно используются для незначительных изменений в стиле.

## <a name="filesfilesmd"></a>[Файлы](files.md)

Обработку файлов в Xamarin.Forms можно выполнить с помощью кода в библиотеке .NET Standard или внедренных ресурсов.

## <a name="gesturesgesturesindexmd"></a>[Жесты](gestures/index.md)

Класс [`GestureRecognizer`](xref:Xamarin.Forms.GestureRecognizer) Xamarin.Forms поддерживает жесты касания, сжатия и сдвига в элементах управления пользовательского интерфейса.

## <a name="localizationlocalizationindexmd"></a>[Локализация](localization/index.md)

Встроенную платформу локализации .NET можно использовать в кроссплатформенных многоязыковых приложениях в Xamarin.Forms.

## <a name="local-databasesdatabasesmd"></a>[Локальные базы данных](databases.md)

Xamarin.Forms поддерживает приложения на основе базы данных с использованием ядра СУБД SQLite, которое позволяет загружать и сохранять объекты в общем коде.

## <a name="messaging-centermessaging-centermd"></a>[Центр обмена сообщениями](messaging-center.md)

Класс `MessagingCenter` в Xamarin.Forms позволяет моделям представлений и другим компонентам взаимодействовать, не зная ничего друг о друге, кроме простого контракта сообщения.

## <a name="navigationnavigationindexmd"></a>[Навигация](navigation/index.md)

Xamarin.Forms предоставляет ряд различных способов перехода по страницам в зависимости от используемого типа объекта `Page`.

## <a name="shellshellmd"></a>[Оболочка](shell.md)

Оболочка Xamarin.Forms — это контейнер для приложений, который предоставляет фундаментальные компоненты пользовательского интерфейса, необходимые для большинства приложений, позволяя сосредоточиться на базовой рабочей нагрузке приложения.

## <a name="templatestemplatesindexmd"></a>[Шаблоны](templates/index.md)

Шаблоны элементов управления позволяют легко настраивать темы на страницах приложений в среде выполнения, тогда как с помощью шаблонов данных можно определять представление данных в поддерживаемых элементах управления.

## <a name="triggerstriggersmd"></a>[Триггеры](triggers.md)

Обновление элементов управления путем ответа на изменения свойств и события в XAML.


## <a name="related-links"></a>Связанные ссылки

- [Введение в Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
