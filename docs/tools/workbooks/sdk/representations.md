---
title: Представления в Xamarin Workbooks
description: В этом документе описывается Xamarin Workbooks представление конвейер, который обеспечивает отрисовку форматированного результаты для любой код, который возвращает значение.
ms.prod: xamarin
ms.assetid: 5C7A60E3-1427-47C9-A022-720F25ECB031
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: d9aafbe13e06875b6577a4d2308e419932fd1589
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103716"
---
# <a name="representations-in-xamarin-workbooks"></a>Представления в Xamarin Workbooks

## <a name="representations"></a>Представления

В рамках сеанса книги или инспектора код, который выполняется и результатом выполнения (например, метод, возвращающий значение или результат выражения) обрабатывается посредством конвейера представления в агенте. Все объекты, за исключением примитивы, такие как целые числа, будут отражены для создания графов интерактивных элементов и проходит через процесс для предоставления альтернативных представлений, которые клиент может отображать большим количеством параметров. Любого размера и глубины безопасно поддерживает объекты (включая циклов и бесконечный перечислимых значений) из-за отложенной и интерактивные отражения и удаленное взаимодействие.

Xamarin Workbooks предоставляет несколько общих типов для всех агентов, а клиенты, которые обеспечивают широкие возможности подготовки к просмотру результатов. [`Color`][xir-color] является одним из примеров такого типа, где для примера в iOS, агент отвечает за преобразование `CGColor` или `UIColor` объектов в `Xamarin.Interactive.Representations.Color` объекта.

Помимо стандартных представлений интеграции пакета SDK предоставляет интерфейсы API для сериализации пользовательского представления в агенте и визуализации представления в клиенте.

## <a name="external-representations"></a>Внешних представлений

[`Xamarin.Interactive.IAgent.RepresentationManager`][repman] предоставляет возможность регистрировать [ `RepresentationProvider` ] [ repp], который integration необходимо реализовать для преобразования из произвольный объект зависит от формы, для подготовки к просмотру. Необходимо реализовать эти зависит от формы [ `ISerializableObject` ] [ serobj] интерфейс.

Реализация `ISerializableObject` интерфейс добавляет метод сериализации, который точно определяет порядок сериализации объектов. `Serialize` Метод ожидает, что разработчик именно будет указано, какие свойства должны быть сериализованы, и какими будут окончательное имя. Просмотрев `Person` объекта в нашей [`KitchenSink` пример] [образец], мы видим, как это работает:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; }

  // Rest of the code is omitted…

  void ISerializableObject.Serialize (ObjectSerializer serializer)
    => serializer.Property (nameof (Name), Name);
}
```

Если мы хотели предоставить надмножество или подмножество свойств из исходного объекта, это можно сделать с помощью `Serialize`. Например, мы может сделать нечто подобное для предоставления предварительно вычисленные `Age` свойство `Person`:

```csharp
public sealed class Person : ISerializableObject
{
  public string Name { get; set; }
  public DateTime DateOfBirth { get; set; }

  // <snip>

  void ISerializableObject.Serialize (ObjectSerializer serializer)
  {
    serializer.Property (nameof (Name), Name);
    serializer.Property (nameof (DateOfBirth), DateOfBirth);

    // Let's pre-compute an Age property that's the person's age in years,
    // so we don't have to compute it in the renderer.
    var age = (DateTime.MinValue + (DateTime.Now - DateOfBirth)).Year - 1;
    serializer.Property ("Age", age)
  }
}
```

> [!NOTE]
> Интерфейсы API, которые создают `ISerializableObject` непосредственно объекты не обязательно должны быть обработаны `RepresentationProvider`. Если требуется отобразить объект **не** `ISerializableObject`, будет необходимо обработать, поместить его в вашей `RepresentationProvider`.

### <a name="rendering-a-representation"></a>Подготовка к просмотру представление

Модули подготовки отчетов, реализованы в JavaScript и будет иметь доступ к версии JavaScript объекта, представленного через `ISerializableObject`. Также будет иметь копию JavaScript `$type` свойство строки, указывающее имя типа .NET.

Мы рекомендуем использовать TypeScript для интеграции кода клиента, который конечно компилируется в обычный JavaScript. В любом случае пакет SDK предоставляет [типизаций] [ typings] который можно ссылаться непосредственно на TypeScript или просто вручную при записи ваниль JavaScript является предпочтительным.

Точка основной интеграции для подготовки к просмотру `xamarin.interactive.RendererRegistry`:

```js
xamarin.interactive.RendererRegistry.registerRenderer(
  function (source) {
    if (source.$type === "SampleExternalIntegration.Person")
      return new PersonRenderer;
    return undefined;
  }
);
```

Здесь `PersonRenderer` реализует `Renderer` интерфейс. См. в разделе [типизаций] [ typings] для получения дополнительных сведений.

[typings]: https://github.com/xamarin/Workbooks/blob/master/SDK/typings/xamarin-interactive.d.ts
[xir-color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[repman]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[repp]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[serobj]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Serialization.ISerializableObject/
