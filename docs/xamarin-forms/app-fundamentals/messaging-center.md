---
title: Помощью Xamarin.Forms MessagingCenter
description: В этой статье объясняется, как использовать помощью Xamarin.Forms MessagingCenter для отправки и получения сообщений, чтобы уменьшить взаимозависимость между классами, таких как просмотр моделей.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: 7fef4443cacba0fa8bdb8d5df070c4244730b4f5
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675183"
---
# <a name="xamarinforms-messagingcenter"></a>Помощью Xamarin.Forms MessagingCenter

_Платформа Xamarin.Forms включает простую службу обмена сообщениями для отправки и получения сообщений._

<a name="Overview" />

## <a name="overview"></a>Обзор

Xamarin.Forms `MessagingCenter` позволяет просматривать модели и другие компоненты для связи с без необходимости знать ничего друг о друге, помимо простого контракта сообщения.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Как работает помощью MessagingCenter

Состоит из двух частей `MessagingCenter`:

-  **Подписка** - прослушивать сообщения с определенной сигнатуры и выполнения некоторых операций при их получении. Несколько подписчиков могут перехватывать и то же сообщение.
-  **Отправить** -публикации сообщения для прослушивателей, с которым выполняется действие. Если прослушиватели не подписаны сообщения будут проигнорированы.


`MessagingService` Является статическим классом с `Subscribe` и `Send` методы, которые используются в решении.

Сообщения имеют строка `message` параметр, используемый как способ *адрес* сообщений. `Subscribe` И `Send` методы используют универсальные параметры для последующего управления как доставляются сообщения — два сообщения с тем же `message` текста, но аргументы различных универсального типа не будут доставляться в тот же подписчик.

API для `MessagingCenter` прост:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Эти методы описаны ниже.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>С помощью помощью MessagingCenter

Сообщения могут отправляться в результате взаимодействия пользователя (например, нажатие кнопки), системных событий (например, элементы управления, изменения состояния) или некоторые другие инцидента (например, завершение асинхронной загрузки). Подписчики могут быть прочитаны изменить внешний вид пользовательского интерфейса, сохраните данные или активировать иной операции.

### <a name="simple-string-message"></a>Простое строковое сообщение

Самый простой сообщение содержит только строки в `message` параметра. Объект `Subscribe` метод, *прослушивает* простое строковое сообщение показан ниже — Обратите внимание, что универсального типа, указав, должен иметь тип отправителя `MainPage`. Все классы в решении можно подписаться на сообщения, используя следующий синтаксис:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

В `MainPage` класса следующий код *отправляет* сообщение. `this` Параметр является экземпляром `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

Не изменяется, строка указывает *тип сообщений* и используется для определения подписчиков, которые для уведомления. Такого рода сообщения используется для указания, что некоторые произошло событие, такие как «отправка завершена», где требуется отсутствие дополнительных сведений.

### <a name="passing-an-argument"></a>Передача аргументов

Чтобы передать аргумент с сообщением, следует указать аргумент типа в `Subscribe` универсальных аргументов и в сигнатуре действие.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Чтобы отправить сообщение с аргументом, включите параметр универсального типа и значение аргумента в `Send` вызов метода.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

В этом простом примере использует `string` аргумент, но содержит C# может быть передан объект.

### <a name="unsubscribe"></a>Отмена подписки

Объект может отменить подписку на подпись сообщения, таким образом, чтобы будущие сообщения не будут доставлены. `Unsubscribe` Синтаксис метода должны отражать подпись сообщения (поэтому может потребоваться включить параметр универсального типа для аргумента сообщения).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Сводка

Помощью MessagingCenter — это простой способ уменьшить количество связей, особенно между Просмотр моделей. Его можно использовать для отправки и получения простых сообщений или передать аргумент между классами. Классы следует отменить подписку на сообщения, которые больше не хотите получать.


## <a name="related-links"></a>Связанные ссылки

- [MessagingCenterSample](https://developer.xamarin.com/samples/UsingMessagingCenter)
- [Примеры Xamarin.Forms](https://github.com/xamarin/xamarin-forms-samples)
