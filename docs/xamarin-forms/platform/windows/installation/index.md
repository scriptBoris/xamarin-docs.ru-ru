---
title: Проекты установки Windows
description: Старые решения Xamarin.Forms (или которые были созданы в macOS) не будет иметь проектах универсальной платформы Windows, и поэтому в этой статье объясняется, как добавить новый проект универсальной платформы Windows в существующее решение Xamarin.Forms.
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/10/2018
ms.openlocfilehash: fb166b69c76ca4c87746358258d97f1cb81cb301
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50123236"
---
# <a name="setup-windows-projects"></a>Проекты установки Windows

_Добавление новых проектов Windows в существующее решение Xamarin.Forms_

Старые решения Xamarin.Forms (или которые были созданы в macOS) не будет иметь проекты приложений для универсальной платформы Windows (UWP). Таким образом необходимо вручную добавить проект универсальной платформы Windows для создания приложений Windows 10 (UWP).

## <a name="add-a-universal-windows-platform-app"></a>Добавьте универсальный Windows приложение платформы

Вы должны работать под управлением **Visual Studio 2017** на **Windows 10** для создания приложений универсальной платформы Windows. Дополнительные сведения о универсальной платформы Windows, см. в разделе [введение в универсальную платформу Windows](/windows/uwp/get-started/universal-application-platform-guide/).

UWP в Xamarin.Forms 2.1 и более поздние версии и поддерживается Xamarin.Forms.Maps в Xamarin.Forms 2.2 и более поздних версий.

Проверьте <a href="#troubleshooting">Устранение неполадок</a> разделе полезные советы.

Выполните эти инструкции по добавлению приложения универсальной платформы Windows, который будет выполняться на телефонах, планшетных и настольных компьютеров Windows 10.

 1. Щелкните правой кнопкой мыши решение и выберите **Добавить > Новый проект...**  и добавьте **пустое приложение (универсальные Windows)** проекта:

  ![](universal-images/add-wu.png "Добавить диалоговое окно нового проекта")

 2. В **новый проект универсальной платформы Windows** диалоговом окне выберите минимальное и целевой версии Windows 10, приложение будет выполняться на:

  ![](universal-images/target-version.png "Диалоговое окно проекта для создания универсальных Windows платформы")

 3. Правой кнопкой мыши проект UWP и выберите **управление пакетами NuGet...**  и добавьте **Xamarin.Forms** пакета. Убедитесь, что другие проекты в решении также будут обновлены до той же версии пакета Xamarin.Forms.

 4. Убедитесь, что будет создаваться новый проект универсальной платформы Windows **сборки > Configuration Manager** окна (это возможно, не произойти по умолчанию). Такт **построения** и **развернуть** поля для универсального проекта:

  [![](universal-images/configuration-sml.png "Окно диспетчера конфигурации")](universal-images/configuration.png#lightbox "окно диспетчера конфигурации")

 5. Щелкните правой кнопкой мыши проект и выберите **Добавить > ссылка** и создать ссылку на проект Xamarin.Forms приложения (.NET Standard или общий проект).

  ![](universal-images/addref-sml.png "Диалоговое окно диспетчера ссылок")

 6. В проекте UWP, изменить **App.xaml.cs** для включения `Init` вызов метода внутри `OnLaunched` метод около строки 52:

```csharp
// under this line
rootFrame.NavigationFailed += OnNavigationFailed;
// add this line
Xamarin.Forms.Forms.Init (e); // requires the `e` parameter
```

 7. В проекте UWP, изменить **MainPage.xaml** , удалив `Grid` внутри `Page` элемент.

 8. В **MainPage.xaml**, добавьте новый `xmlns` запись для `Xamarin.Forms.Platform.UWP`:

```csharp
xmlns:forms="using:Xamarin.Forms.Platform.UWP"
```

 9. В **MainPage.xaml**, измените значение корневого расположения `<Page` элемент `<forms:WindowsPage`:

```xaml
<forms:WindowsPage
...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
...
</forms:WindowsPage>
```

 10. В проекте UWP, изменить **MainPage.xaml.cs** удаляемый `: Page` наследования спецификатор имени класса (так как он теперь будет наследовать от `WindowsPage` из-за изменений, внесенных на предыдущем шаге):

```csharp
public sealed partial class MainPage  // REMOVE ": Page"
```

 11. В **MainPage.xaml.cs**, добавьте `LoadApplication` вызов в `MainPage` конструктор, чтобы запустить приложение Xamarin.Forms:

```csharp
// below this existing line
this.InitializeComponent();
// add this line
LoadApplication(new YOUR_NAMESPACE.App());
```

<!--
11 . Double-click **Package.appxmanifest** to set these capabilities
  that are often required:

  Capabilities set:

  * Internet (Client)
  * Location
-->

12. Добавьте любые локальные ресурсы (например) файлы изображений) из существующей платформы проектов, которые необходимы.

## <a name="troubleshooting"></a>Устранение неполадок

<a name="target-invocation-exception" />

### <a name="target-invocation-exception-when-using-compile-with-net-native-tool-chain"></a>«Целевое вызов исключения» при использовании «Компиляции с помощью цепочки инструментов машинного кода .NET»

Если приложение UWP ссылается на несколько сборок (например библиотеки элементов управления третьим лицам или само приложение разбивается на несколько библиотек), возможно, Xamarin.Forms не удалось загрузить объекты из этих сборок (например, пользовательские модули подготовки отчетов).

Это может произойти при использовании **компиляцию с параметром цепочка инструментов .NET Native** которого может использоваться для приложений UWP в **свойства > построения > Общие** окно для проекта.

Это можно исправить с помощью универсальной платформы Windows перегрузку из `Forms.Init` вызов в **App.xaml.cs** как показано в приведенном ниже коде (следует заменить `ClassInOtherAssembly` с классом фактические ссылки в коде):

```csharp
// You'll need to add `using System.Reflection;`
List<Assembly> assembliesToInclude = new List<Assembly>();

// Now, add in all the assemblies your app uses
assembliesToInclude.Add(typeof (ClassInOtherAssembly).GetTypeInfo().Assembly);

// Also do this for all your other 3rd party libraries
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
// replaces Xamarin.Forms.Forms.Init(e);
```

Добавьте запись для каждой сборки, добавленный в обозревателе решений, либо через прямую ссылку или NuGet в качестве ссылки.

#### <a name="dependency-services-and-net-native-compilation"></a>Зависимые службы и .NET компиляция в собственном коде

Выпускные построения, с помощью .NET Native, компиляция может завершиться ошибкой для разрешения зависимостей служб, которые определены за пределами основного приложения исполняемый файл (например, в отдельный проект или библиотеку).

Используйте `DependencyService.Register<T>()` метод, чтобы вручную зарегистрировать классы службы зависимостей. Исходя из примера выше, добавьте метод регистрации, следующим образом:

```csharp
Xamarin.Forms.Forms.Init(e, assembliesToInclude);
Xamarin.Forms.DependencyService.Register<ClassInOtherAssembly>(); // add this
```
