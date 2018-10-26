---
title: Настройка параметров памяти Java для Android designer
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 62FAF21C-8090-4AF3-9D88-05A4CFCAFFDC
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/02/2018
ms.openlocfilehash: cf0df42ba398944a99cc4179b94f0d3cb8ba503e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118062"
---
# <a name="adjusting-java-memory-parameters-for-the-android-designer"></a>Настройка параметров памяти Java для Android designer

Параметры памяти по умолчанию, которые используются при запуске `java` процесс Android designer могут оказаться несовместимыми с некоторых конфигураций системы.

Начиная с Xamarin Studio 5.7.2.7 (и более поздних версий, Visual Studio для Mac) и Visual Studio Tools для Xamarin 3.9.344, эти параметры можно настраивать на каждого проекта.

## <a name="new-android-designer-properties-and-corresponding-java-options"></a>Новые свойства Android конструктора и соответствующие параметры Java

Следующие имена свойств соответствуют указанной java [параметр командной строки](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/java.html)

- **AndroidDesignerJavaRendererMinMemory** - Xms

- **AndroidDesignerJavaRendererMaxMemory** - Xmx

- **AndroidDesignerJavaRendererPermSize** -XX:MaxPermSize


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1.  Откройте решение в Visual Studio.

2.  Выберите каждый проект Android по одному в обозревателе решений и нажмите кнопку [Показать все файлы](https://msdn.microsoft.com/en-us/library/4afxey9h.aspx) дважды для каждого проекта. Вы можете пропустить проектов, которые не содержат `.axml` файлы макета. Этот шаг гарантирует, что каждый каталог проекта содержит `.csproj.user` файла.

3.  Выйдите из Visual Studio.

4.  Найдите `.csproj.user` файл для каждого из проектов из шага 2.

5.  Измените каждый `.csproj.user` файл в текстовом редакторе.

6.  Добавьте одно или все новые свойства Android конструктора памяти в пределах `<PropertyGroup>` элемент. Можно использовать существующий `<PropertyGroup>` или создайте новую. Ниже приведен полный пример `.csproj.user` файл, который содержит все атрибуты 3, задайте их значения по умолчанию:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="12.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
       <PropertyGroup>
         <ProjectView>ProjectFiles</ProjectView>
       </PropertyGroup>
       <PropertyGroup>
         <AndroidDesignerJavaRendererMinMemory>128m</AndroidDesignerJavaRendererMinMemory>
         <AndroidDesignerJavaRendererMaxMemory>750m</AndroidDesignerJavaRendererMaxMemory>
         <AndroidDesignerJavaRendererPermSize>350m</AndroidDesignerJavaRendererPermSize>
       </PropertyGroup>
    </Project>
    ```

7.  Сохраните и закройте все обновленные `.csproj.user` файлов.

8.  Перезапустите Visual Studio и открыв решение повторно.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1.  Откройте решение в Visual Studio для Mac, чтобы в каталоге решения содержит `.userprefs` файла.

2.  Выйдите из Visual Studio для Mac.

3.  Найдите `.userprefs` файл в каталоге решения.

4.  Изменить `.userprefs` файл в текстовом редакторе.

5.  Найдите элемент XML в следующем формате. Последняя часть имени этого элемента будет совпадать с именем проекта: «AndroidApplication1» в этом примере:

    ```xml
    <MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >
    ```

6.  Если `<MonoDevelop.Ide.ItemProperties.AndroidApplication1 ... >` элемент не существует, создайте его в любом месте внутри содержащего его `<Properties>` элемент. Обязательно замените «AndroidApplication1» с именем проекта.

7.  Добавьте одно или все новые свойства Android конструктора памяти как атрибуты элемента. Ниже приведен полный пример `.userprefs` файл, который содержит все атрибуты 3, задайте их значения по умолчанию:

    ```xml
    <Properties StartupItem="AndroidApplication1\AndroidApplication1.csproj">
      <MonoDevelop.Ide.Workspace ActiveConfiguration="Debug" PreferredExecutionTarget="Android.SelectDevice" />
      <MonoDevelop.Ide.Workbench />
      <MonoDevelop.Ide.DebuggingService.Breakpoints>
        <BreakpointStore />
      </MonoDevelop.Ide.DebuggingService.Breakpoints>
      <MonoDevelop.Ide.DebuggingService.PinnedWatches />
      <MonoDevelop.Ide.ItemProperties.AndroidApplication1 AndroidDesignerJavaRendererMinMemory="128m" AndroidDesignerJavaRendererMaxMemory="750m" AndroidDesignerJavaRendererPermSize="350m" />
    </Properties>
    ```

8.  Повторите шаги 5 – 7 для каждого проекта Android в решение, которое содержит любые `.axml` файлы макета. (То есть, добавьте один `<MonoDevelop.Ide.ItemProperties.ProjectName>` элемент для каждого проекта.)

9.  Сохраните и закройте `.userprefs` файл.

10. Перезапустите Visual Studio для Mac и повторно откройте решение.

-----

