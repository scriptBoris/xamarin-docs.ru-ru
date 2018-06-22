---
title: Устраните проблемы, связанные с System.Diagnostics.Tracing и потоков данных TPL
description: 'PCL практический пример: как устранить проблемы, связанные с System.Diagnostics.Tracing для пакета NuGet потоков данных TPL Майкрософт?'
ms.prod: xamarin
ms.assetid: 7986A556-382D-4D00-ACCF-3589B4029DE8
ms.date: 04/17/2018
author: asb3993
ms.author: amburns
ms.openlocfilehash: 1acc9ccc78ad14198a59e74d1fae845790d66b16
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918530"
---
# <a name="pcl-case-study-how-can-i-resolve-problems-related-to-systemdiagnosticstracing-for-the-microsoft-tpl-dataflow-nuget-package"></a>PCL практический пример: как устранить проблемы, связанные с System.Diagnostics.Tracing для пакета NuGet потоков данных TPL Майкрософт?

> [!IMPORTANT]
> Этот пример определенного `System.Diagnostic.Tracing` больше не создает ошибок по умолчанию в последних версиях Xamarin. Во время Предлагаемый обходной путь, по-прежнему будет работать, обратите внимание, что некоторые из ошибок, перечисленных в разделе «уровни ошибок» исправлены.
> Кроме того следует отметить, что .NET Standard стала наиболее предпочтительным способом реализации API-интерфейсы .NET кросс платформенных.

## <a name="summary"></a>Сводка

Xamarin.iOS и Xamarin.Android, не следует реализовывать каждый профиль PCL, они позволяют как ссылки на 100%. Для удобства практической работы в Visual Studio для Mac, Visual Studio и диспетчер пакетов NuGet проектов Xamarin позволяют использовать несколько профилей, которые имеют только _неполные_ реализации. Например, не Xamarin.iOS и Xamarin.Android в настоящее время содержит полную реализацию типов в «System.Diagnostics.Tracing» PCL пространства имен. Это ограничение приводит к три уровня ошибок при попытке использовать значение по умолчанию `portable-net45+win8+wpa81` версию пакета NuGet потоков данных TPL Microsoft.

## <a name="workaround-switch-the-app-project-to-reference-the-portable-net45win8wp8wpa81-version-of-the-tpl-dataflow-library"></a>Обходной путь: Переключить проект приложения для ссылки на `portable-net45+win8+wp8+wpa81` версию библиотеки потоков данных TPL

(Это позволяет избежать все три уровня ошибок и работает для всех последних версиях Xamarin.)

1. Откройте проект приложения **.csproj** файл в текстовом редакторе.

2. Найдите строку, которая выглядит примерно так:

    ```xml
    <HintPath>..\packages\Microsoft.Tpl.Dataflow.4.5.24\lib\portable-net45+win8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

3. Изменение `portable-net45+win8+wpa81` для `portable-net45+win8+wp8+wpa81` (`+wp8` добавляется):

    ```xml
    <HintPath>..\packages\System.Threading.Tasks.Dataflow.4.5.25\lib\portable-net45+win8+wp8+wpa81\System.Threading.Tasks.Dataflow.dll</HintPath>
    ```

### <a name="explanation"></a>Объяснение

`portable-net45+win8+wp8+wpa81` Не ссылается на версию библиотеки **System.Diagnostics.Tracing.dll** _вообще_, поэтому она полностью позволяет избежать проблем всех трех слоев.

### <a name="limitations"></a>Ограничения

- `portable-net45+win8+wp8+wpa81` Версию библиотеки могут не включать 100% функциональные возможности `portable-net45+win8+wpa81` версии.

- Диспетчер пакетов NuGet устанавливает `portable-net45+win8+wpa81` версию пакета PCL NuGet по умолчанию, поэтому необходимо вручную изменить ссылку.

## <a name="details-about-the-three-layers-of-errors"></a>Сведения о трех уровней ошибок

1. **System.Diagnostics.Tracing.dll** видимой сборкой в данный момент отсутствуют все версии Mac Xamarin.Android (появляется ошибка неоткрытые 34888) и исключаются из всех версий Xamarin.iOS ниже, чем 9.0 (или меньше, чем XamarinVS 3.11.1443 в Windows) (устранена в [ошибки 32388](https://bugzilla.xamarin.com/show_bug.cgi?id=32388)). Этой проблемы возникает одна из следующих ошибок в зависимости от цели развертывания и компоновщик параметры:

    - Xamarin.Android.Common.targets: Ошибка: возникло исключение при загрузке сборки: System.IO.FileNotFoundException: не удалось загрузить сборку "System.Diagnostics.Tracing, версия = 4.0.0.0, язык и региональные параметры = neutral, PublicKeyToken = b03f5f7f11d50a3a". Возможно он не существует в моно для Android профиля?

    - Не удалось загрузить файл или сборку «System.Diagnostics.Tracing» или одну из ее зависимостей. Не удается найти указанный файл. (System.IO.FileNotFoundException)

    - MTOUCH: ошибка MT3001: удалось AOT сборки "/ Users/macuser/Projects/TPLDataflow/UnifiedSingleViewIphone1/obj/iPhone/Debug/mtouch-cache/64/Build/System.Threading.Tasks.Dataflow.dll"

    - MTOUCH: ошибка MT2002: не удалось разрешить сборку: "System.Diagnostics.Tracing, версия = 4.0.0.0, язык и региональные параметры = neutral, PublicKeyToken = b03f5f7f11d50a3a"

2. Текущий [реализацию Mono типов в «System.Diagnostics.Tracing»](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) не удалось обнаружить некоторые перегрузки метода ([ошибки 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Эта проблема возникнет одна из следующих ошибок компоновщика при создании приложения Xamarin.

    - / Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: ошибка: ошибка при выполнении задач LinkAssemblies: ошибка XA2006: ссылка на элемент метаданных "System.Void System.Diagnostics.Tracing.EventSource:: WriteEvent(System.Int32,System.Object[]) "(определенные в" System.Threading.Tasks.Dataflow, версия = 4.5.24.0, язык и региональные параметры = neutral, PublicKeyToken = b03f5f7f11d50a3a ") из" System.Threading.Tasks.Dataflow, версия = 4.5.24.0, язык и региональные параметры = neutral, PublicKeyToken = b03f5f7f11d50a3a "не удалось разрешить.

    - MTOUCH: ошибка MT2002: не удалось разрешить ссылку «System.Void System.Diagnostics.Tracing.EventSource::WriteEvent(System.Int32,System.Object[])» из «System.Diagnostics.Tracing, версия = 4.0.0.0, язык и региональные параметры = neutral, PublicKeyToken = b03f5f7f11d50a3a»

3. Текущий [реализацию Mono типов в «System.Diagnostics.Tracing»](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) также является в настоящее время _пустой_ «пустой» реализация ([ошибки 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890)). Таким образом, любые попытки использовать эти методы во время выполнения может дать непредвиденные результаты. Для _определенной_ случай библиотеки потоков данных TPL Microsoft, похоже на вызовы `WriteEvent(System.Int32,System.Object[])` не нужны для большинства поведение библиотеки поэтому исправление «уровень 2» ([ошибки 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337), Добавление пустой скорее всего будет достаточно для большинства вариантов использования потоков данных TPL Microsoft реализации).

## <a name="questions--answers"></a>Вопросы и ответы

### <a name="i-was-able-to-leave-linking-enabled-with-the-codeportable-net45win8wpa81code-version-of-the-library-on-older-versions-of-xamarinios-or-on-xamarinandroid-how-did-that-work"></a>Возможность оставить связывания с включенными <code>portable-net45+win8+wpa81</code> версию библиотеки на более старые версии Xamarin.iOS или Xamarin.Android. Как это поможет?

#### <a name="answer"></a>Ответов

Это _возможных_ для получения сборки для завершения «успешно» (с компоновки включена) в более ранних версий Xamarin.iOS или Xamarin.Android на Mac при включении ссылку на `System.Diagnostics.Tracing.dll` _ссылку на сборку_ \[1\] вместо _видимой сборкой_ \[2], но к сожалению это делается не «правильный». Ссылочные сборки предназначены только для использования при построении _переносимые библиотеки_, код не платформой как приложения. Попытка _запуска_ кода, содержащегося в ссылочные сборки (а не просто сборки для нее) может привести к непредвиденным результатам. Правильным решением будет моно команды для добавления отсутствующего `WriteEvent(System.Int32,System.Object[])` перегруженные методы [ `EventSource` ](https://github.com/mono/mono/blob/master/mcs/class/corlib/System.Diagnostics.Tracing/EventSource.cs) типа ([ошибки 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337)). Для теперь оптимальным является переключение `portable-net45+win8+wp8+wpa81` версию библиотеки потоков данных TPL Microsoft, как описано в предыдущем разделе решение.

(Для тех, кто может прочитать данную статью после просмотра связанных более старые, более компактно ответов из StackOverflow (<http://stackoverflow.com/a/23591322/2561894>), обратите внимание, что было различие между ссылочных сборок и сборке фасадной _не_ упоминалось существует.)

**\[1\] расположения «Ссылочные сборки»**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\System.Diagnostics.Tracing.dll`

Mac (моно): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/System.Diagnostics.Tracing.dll`

**\[2\] расположения «assembly Facade»**

Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

Mac (моно): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`


### <a name="will-it-help-if-i-manually-add-a-reference-to-the-systemdiagnosticstracing-facade-assembly"></a>Он поможет, если вручную добавить ссылку на сборку фасадной «System.Diagnostics.Tracing»?

_В частности можно устранить эту проблему, с помощью следующей процедуры 2?_

1. _Копировать `System.Diagnostics.Tracing.dll` видимой сборкой в папку проекта приложения из одного из следующих расположений:_

    Windows: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.5\Facades\System.Diagnostics.Tracing.dll`

    Mac (моно): `/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/4.5/Facades/System.Diagnostics.Tracing.dll`

2. _Добавьте ссылку на сборку фасадной в проекте приложения Xamarin.iOS и Xamarin.Android._

#### <a name="answer"></a>Ответов

Нет, это не поможет.

- Для Xamarin.iOS 9.0 или любой последние версии Xamarin.Android в Windows этот обходной путь является строго избыточным и может привести к ошибкам компиляции аналогично «сборки «System.Diagnostics.Tracing» с тем же идентификатором уже импортирован.».

- Поможет Xamarin.iOS 8.10 или ниже или Xamarin.Android на Mac, этот обходной путь, но _только_ для сборки проблемы отсутствующего «уровень 1». Он будет _не_ решения ошибках компоновщика «уровень 2» не является комплексное решение.

### <a name="can-i-use-the-systemdiagnosticstracing-nuget-packagehttpswwwnugetorgpackagessystemdiagnosticstracing-to-solve-the-problem"></a>Можно использовать [пакет System.Diagnostics.Tracing NuGet](https://www.nuget.org/packages/System.Diagnostics.Tracing/) Чтобы устранить эту проблему?

#### <a name="answer"></a>Ответов

Нет, «System.Diagnostics.Tracing» пакета NuGet 3.0 включает только реализации специфический для платформы «DNXCore50» и «netcore50». Он явно _опускает_ реализации («MonoAndroid») Xamarin.Android и Xamarin.iOS («MonoTouch» и «xamarinios»). Это означает, что установка пакета будет иметь _не влияет_ для проектов Xamarin.Android и Xamarin.iOS. Пакет NuGet предполагает, что обе эти платформы предоставляют свои _собственной_ реализации типов. Это предположение «правильный», в том смысле, что у _моно_ реализации пространства имен, а также как описано в разделе точек \#2 и \#3 из «Сведения о трех уровней ошибок» выше, Реализация в настоящее время неполон. Таким образом, соответствующие исправления для устранения моно командой [ошибки 27337](https://bugzilla.xamarin.com/show_bug.cgi?id=27337) и [ошибки 34890](https://bugzilla.xamarin.com/show_bug.cgi?id=34890).

## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной помощи, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения см. в разделе [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах контактов, предложения, а также как файл новую ошибку, при необходимости.
