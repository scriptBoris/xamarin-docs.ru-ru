---
title: Приступая к работе с Xamarin Workbooks SDK
description: В этом документе описывается, как приступить к работе с книгами пакет SDK Xamarin, который можно использовать для разработки интеграция с Xamarin Workbooks.
ms.prod: xamarin
ms.assetid: FAED4445-9F37-46D8-B408-E694060969B9
author: lobrien
ms.author: laobri
ms.date: 03/30/2017
ms.openlocfilehash: 5800e98acbff147735ae4a6125979a4b47be2367
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50115371"
---
# <a name="getting-started-with-the-xamarin-workbooks-sdk"></a>Приступая к работе с Xamarin Workbooks SDK

Этот документ содержит краткое руководство по началу работы с разработкой интеграция с Xamarin Workbooks. Большая часть это будет работать с стабильной Xamarin Workbooks, но **загрузка интеграции посредством пакетов NuGet поддерживается только в версии 1.3 книг**, в альфа-канал на момент написания этой статьи.

## <a name="general-overview"></a>«Общие»

Xamarin Workbooks интеграции являются небольшой библиотеки, использующие [ `Xamarin.Workbooks.Integrations` NuGet] [ nuget] пакета SDK для интеграции с Xamarin Workbooks и Inspector агентов предоставляют расширенные возможности.

Существует 3 основных шага по начало разработки интеграцию, мы будем структуры их здесь.

## <a name="creating-the-integration-project"></a>Создание проекта служб Integration

Библиотеки интеграции лучше всего разрабатываются в виде многоплатформенного библиотеки. Так как вы хотите предоставить рекомендации интеграции на всех доступных агентов, прошлое и будущее, нужно будет выбрать широко поддерживается набор библиотек. Рекомендуется использовать шаблон «Переносимой библиотеки» для самую широкую поддержку:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Переносимая библиотека шаблонов Visual Studio для Mac](images/xamarin-studio-pcl.png)](images/xamarin-studio-pcl.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Visual Studio шаблона переносимой библиотеки](images/visual-studio-pcl.png)](images/visual-studio-pcl.png#lightbox)

В Visual Studio необходимо убедиться, что выбраны следующие целевых платформ для переносимой библиотеки:

[![Переносимая библиотека платформы Visual Studio](images/visual-studio-pcl-platforms.png)](images/visual-studio-pcl-platforms.png#lightbox)

-----

После создания проекта библиотеки, добавьте ссылку на наш `Xamarin.Workbooks.Integration` библиотеки NuGet через диспетчер пакетов NuGet.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![NuGet Visual Studio для Mac](images/xamarin-studio-nuget.png)](images/xamarin-studio-nuget.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![NuGet Visual Studio](images/visual-studio-nuget.png)](images/visual-studio-nuget.png#lightbox)

-----

Необходимо удалить пустой класс, который создается автоматически в рамках проекта — вам не требуется она для этого. Если вы выполнили следующие действия, вы будете готовы приступить к созданию интеграции.

## <a name="building-an-integration"></a>Построение интеграции

Мы создадим простую интеграцию. Мы очень любим цвет зеленый цвет, поэтому мы добавим зеленый цвет как представление для каждого объекта. Во-первых, создайте новый класс с именем `SampleIntegration`и сделать его реализации наших [ `IAgentIntegration` ] [ integration-type] интерфейса:

```csharp
using Xamarin.Interactive;

public class SampleIntegration : IAgentIntegration
{
    public void IntegrateWith (IAgent agent)
    {
    }
}
```

Нам нужно, – это добавить [представление](~/tools/workbooks/sdk/representations.md) для каждого объекта, который имеет зеленый цвет. Мы сделаем это с помощью поставщика представление. Поставщики наследуют от [ `RepresentationProvider` ] [ reppr] класс — для нас, нам нужно переопределить [ `ProvideRepresentations` ] [ prrep]:

```csharp
using Xamarin.Interactive.Representations;

class SampleRepresentationProvider : RepresentationProvider
{
    public override IEnumerable<object> ProvideRepresentations (object obj)
    {
        // This corresponds to Pantone 2250 XGC, our favorite color.
        yield return new Color (0.0, 0.702, 0.4314);
    }
}
```

Мы возвращая [ `Color` ] [ color], предварительно созданные тип представления в наш пакет SDK.
Вы заметите, что тип возвращаемого значения является `IEnumerable<object>` &mdash;один поставщик представление может возвращать множество представлений объекта! Все поставщики представление вызываются для каждого объекта, поэтому очень важно не делать никаких предположений о том, какие объекты передаются для вас.

Последним шагом является фактически регистрация наш поставщик с агентом и указать книг, где найти тип нашей интеграции. Чтобы зарегистрировать поставщик, добавьте следующий код в `IntegrateWith` метод в `SampleIntegration` класс, созданный ранее:

```csharp
agent.RepresentationManager.AddProvider (new SampleRepresentationProvider ());
```

Установка интеграции типа выполняется через атрибут на уровне сборки. Позволяет разместить их в ваш AssemblyInfo.cs или в том же классе, тип интеграции для удобства:

```csharp
[assembly: AgentIntegration (typeof (SampleIntegration))]
````

Во время разработки, может оказаться удобнее использовать [ `AddProvider` перегрузки] [ addprovider] на `RepresentationManager` , позволяющие зарегистрировать обратный вызов простой для предоставления представлений в книге в а затем переместите этот код в вашей `RepresentationProvider` реализации после завершения работы. Пример для подготовки к просмотру [ `OxyPlot` ] [ oxyplot] `PlotModel` может выглядеть следующим образом:

```csharp
InteractiveAgent.RepresentationManager.AddProvider<PlotModel> (
  plotModel => Image (new SvgExporter {
      Width = 300,
      Height = 250
    }.ExportToString (plotModel)));
```

> [!NOTE]
> Эти API-интерфейсы позволяют быстро приступить к работе, но не рекомендуется доставки всей интеграцию только с их помощью&mdash;они предоставляют очень мало контроль над обработкой типов, если клиент.

Представлением зарегистрирован интеграция в продаже!

## <a name="shipping-your-integration"></a>Доставка интеграции

Для отправки интеграции, необходимо добавить его в пакет NuGet.
Можно доставить ее с помощью существующей библиотеки NuGet, или при создании нового пакета, можно использовать этот шаблон файла nuspec отправной точки.
Необходимо заполнить разделы, относящиеся к интеграции. Наиболее важной частью является, что все файлы для своей интеграции должен быть в `xamarin.interactive` находящемся в корне пакета. Это позволяет нам легко найти все соответствующие файлы для своей интеграции, независимо от того, можно использовать существующий пакет или создайте новую.

```xml
<?xml version="1.0"?>
<package xmlns="http://schemas.microsoft.com/packaging/2010/07/nuspec.xsd">
    <metadata>
      <id>$YourNuGetPackage$</id>
      <version>$YourVersion$</version>
      <authors>$YourNameHere$</authors>
      <projectUrl>$YourProjectPage$</projectUrl>
      <description>A short description of your library.</description>
    </metadata>
    <files>
      <file src="Path\To\Your\Integration.dll" target="xamarin.interactive" />
    </files>
</package>
```

После создания файла nuspec, можно упаковать NuGet следующим образом:

```csharp
nuget pack MyIntegration.nuspec
```

а затем опубликовать его, чтобы [NuGet][nugetorg]. Как только есть, вы сможете сослаться на него из любой книги и Опробуйте в работе. На следующем снимке экрана мы упаковано пример интеграции, мы в этом документе и установки пакета NuGet в книге:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Книги с интеграцией](images/mac-workbooks-integrated.png)](images/mac-workbooks-integrated.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Книги с интеграцией](images/windows-workbooks-integrated.png)](images/windows-workbooks-integrated.png#lightbox)

-----

Обратите внимание на то, что вы не видите любой `#r` директивы или что-нибудь для инициализации интеграции — книг Разобравшись все это для вас в фоновом!

## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с нашей документацией, Дополнительные сведения о движущие, входящие в состав пакета SDK, и мы [пример интеграции](~/tools/workbooks/samples/index.md) для дополнительных задач, которые можно выполнить из интеграции, такие как предоставление пользовательским кодом JavaScript, которая выполняется в Клиент книг.

[integration-type]: https://developer.xamarin.com/api/type/Xamarin.Interactive.IAgentIntegration/
[repman-api]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.IRepresentationManager/
[color]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.Color/
[xir]: https://developer.xamarin.com/api/namespace/Xamarin.Interactive.Representations/
[reppr]: https://developer.xamarin.com/api/type/Xamarin.Interactive.Representations.RepresentationProvider/
[prrep]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.RepresentationProvider.ProvideRepresentations/p/System.Object/
[nugetorg]: https://nuget.org
[nuget]: https://nuget.org/packages/Xamarin.Workbooks.Integration
[addprovider]: https://developer.xamarin.com/api/member/Xamarin.Interactive.Representations.IRepresentationManager.AddProvider/
[oxyplot]: http://www.oxyplot.org/
