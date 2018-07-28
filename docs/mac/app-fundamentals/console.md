---
title: Использование привязок Xamarin.Mac для консольных приложений
description: Создание автономных консольного приложения с помощью Xamarin.Mac, для доступа к macOS собственного API-интерфейсы.
ms.prod: xamarin
ms.assetid: 9E52B4CC-F530-4B3E-984A-7F5719A6D528
ms.technology: xamarin-mac
author: migueldeicaza
ms.author: miguel
ms.date: 07/27/2018
ms.openlocfilehash: 135ef06cd044235023c3acc970c8e7a33f144b47
ms.sourcegitcommit: d0da5ce4158239abd2b36f67550e9b475055766a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39320833"
---
# <a name="xamarinmac-bindings-in-console-apps"></a>Xamarin.Mac привязок в консольных приложениях

Существует несколько сценариев, где вы хотите использовать некоторые из Apple собственные API-интерфейсы в C# создать автономное приложение &ndash; , не поддерживает пользовательский интерфейс &ndash; с помощью C#.

Шаблоны проектов для приложений для Mac добавлен вызов `NSApplication.Init()` следуют вызов `NSApplication.Main(args)`, обычно выглядит следующим образом:

```csharp
static class MainClass {
    static void Main (string [] args)
    {
        NSApplication.Init ();
        NSApplication.Main (args);
    }
}
```

Вызов `Init` подготавливает среду выполнения Xamarin.Mac, вызов `Main(args)` запускает Cocoa приложения основной цикл, который подготавливает приложение для получения событий клавиатуры и мыши и показывать главное окно приложения.   Вызов `Main` приведет к попытке поиска Cocoa ресурсов, подготовьте окна верхнего уровня и ожидает, что программа частью пакета приложения (программы, распределенных в каталоге с `.app` расширение и очень определенного макета).

Пользователь не обязательно автономных приложений интерфейсов и не обязательно должны выполняться как часть пакета приложения.

## <a name="creating-the-console-app"></a>Создание консольного приложения

Поэтому лучше начать с обычного типа проект консоли .NET.

Вам потребуется выполнить ряд действий.

- Создание пустого проекта.
- Справочник по библиотеке Xamarin.Mac.dll.
- Перенесите неуправляемой зависимости в проект.

Эти действия описаны более подробно ниже:

### <a name="create-an-empty-console-project"></a>Создайте пустой проект консоли

Создайте проект консольного приложения .NET, убедитесь, что это .NET и не .NET Core, как Xamarin.Mac.dll не запускается в среде выполнения .NET Core, оно работает только с помощью среды выполнения Mono.

### <a name="reference-the-xamarinmac-library"></a>Справочник по библиотеке Xamarin.Mac

Чтобы скомпилировать код, необходимо ссылаться на `Xamarin.Mac.dll` сборки из этого каталога: `/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full`

Чтобы сделать это, перейдите в ссылки проекта, выберите **сборку .NET** , а щелкните **Обзор** кнопку, чтобы найти файл в файловой системе.  Перейдите по пути выше, а затем выберите **Xamarin.Mac.dll** из этого каталога.

Это даст вам доступ к интерфейсам API Cocoa во время компиляции.   На этом этапе можно добавить `using AppKit` в верхнюю часть файла и вызовите метод `NSApplication.Init()` метод.   Перед запуском приложения имеется только одно действие.

### <a name="bring-the-unmanaged-support-library-into-your-project"></a>Перевести поддержки неуправляемые библиотеки в проект

Прежде чем приложение будет выполняться, необходимо перевести `Xamarin.Mac` библиотека поддержки в проект.   Чтобы сделать это, добавьте новый файл в проект (в параметрах проекта, выберите **добавить**, а затем **Добавление существующего файла**) и перейдите в этот каталог:

`/Library/Frameworks/Xamarin.Mac.framework/Versions/Current/lib`

Здесь, выберите файл **libxammac.dylib**.   Вам будет предложено выбрать копирование, связывание или перемещение.   Лично, мне нравится компоновка, но копирование также будет работать.    Затем необходимо выбрать файл и на панели свойств (выберите **представление > панели > свойства** Если панель свойств не отображается), перейдите в меню **построения** разделе и задайте **Копировать в выходной каталог Каталог** присвоить **копировать, если новее**.

Теперь вы можете запустить приложения Xamarin.Mac.

В каталоге bin результат будет выглядеть следующим образом:

```
Xamarin.Mac.dll
Xamarin.Mac.pdb
consoleapp.exe
consoleapp.pdb
libxammac.dylib
```

Чтобы запустить это приложение, необходимо будет все эти файлы в одном каталоге.

## <a name="building-a-standalone-application-for-distribution"></a>Создание автономного приложения для распространения

Может потребоваться распространить один исполняемый файл для пользователей.  Чтобы сделать это, можно использовать `mkbundle` средство, чтобы включить различные файлы в автономный исполняемый файл.

Во-первых убедитесь, что приложение компилируется и выполняется.   Когда вы будете удовлетворены результатами, можно выполнить следующую команду из командной строки:

```
$ mkbundle --simple -o /tmp/consoleapp consoleapp.exe --library libxammac.dylib --config /Library/Frameworks/Mono.framework/Versions/Current/etc/mono/config --machine-config /Library/Frameworks/Mono.framework/Versions/Current//etc/mono/4.5/machine.config
[Output from the bundling tool]
$ _
```

При вызове выше командной строки, параметр `-o` — используется для указания созданные выходные данные, в этом случае мы передали `/tmp/consoleapp`.   Теперь это автономное приложение, которое можно распространять и не имеет внешних зависимостей на Mono или Xamarin.Mac, это является полностью автономным исполняемым файлом.

Указанная командная строка вручную **machine.config** файл для использования и файл конфигурации сопоставления библиотеки всей системы.   Они не нужны для всех приложений, но будет удобнее объединить их, так как они понадобятся при использовании дополнительные возможности .NET

## <a name="project-less-builds"></a>Сборки проекта без участия

Необходим полный проект для создания автономного приложения Xamarin.Mac, также можно использовать простые файлы makefile Unix для выполнения задачи.   В следующем примере показано, как вы можете настроить файла makefile для приложения простой командной строки:

```
XAMMAC_PATH=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib/x86_64/full/
DYLD=/Library/Frameworks/Xamarin.Mac.framework/Versions/Current//lib
MONODIR=/Library/Frameworks/Mono.framework/Versions/Current/etc/mono

all: consoleapp.exe

consoelapp.exe: consoleapp.cs Makefile
    mcs -g -r:$(XAMMAC_PATH)/Xamarin.Mac.dll consoleapp.cs
    
run: consoleapp.exe
    MONO_PATH=$(XAMMAC_PATH) DYLD_LIBRARY_PATH=$(DYLD) mono --debug consoleapp.exe $(COMMAND)


bundle: consoleapp.exe
    mkbundle --simple consoleapp.exe -o ncsharp -L $(XAMMAC_PATH) --library $(DYLD)/libxammac.dylib --config $(MONODIR)/config --machine-config $(MONODIR)/4.5/machine.config
```

Выше `Makefile` предоставляет три задачи:

- `make` построит программы
- `make run` построить и запустить программу из текущего каталога
- `make bundle` будет создан автономный исполняемый файл
