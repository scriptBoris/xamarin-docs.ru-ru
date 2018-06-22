---
title: Можно изменить путь выходного файла IPA
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 9c80a209279a2f032eb6c9efcba1398ca0e267a5
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2018
ms.locfileid: "31882806"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Можно изменить путь выходного файла IPA

## <a name="for-cycle-7-and-higher"></a>Для цикла 7 и выше
Да, для этого можно использовать настраиваемые целевые объекты MSBuild. Самый простой вариант — возможно, копирование `.ipa` файла после его построения.

Эти шаги будут работать для любого проекта iOS подсистемой сборки MSBuild на Mac и Windows. (Примечание: все проекты единой API используют ядро сборки MSBuild.)

1. Откройте `.csproj` в файле проекта приложения iOS в текстовом редакторе, а затем добавьте следующие строки в конец (непосредственно перед закрывающим тегом `</Project>` тега):
    
    ```
    <PropertyGroup>
           <CreateIpaDependsOn>
           $(CreateIpaDependsOn);
            CopyIpa
           </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
        Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. Значение DestinationFolder требуемой папке выходных данных. В обычном режиме может использовать свойства MSBuild (например, $(OutputPath)) в пределах этого аргумента при необходимости.

## <a name="notes"></a>Примечания
- `CreateIpaDependsOn` Свойство определено в `Xamarin.iOS.Common.targets` файл, который является частью Xamarin.iOS. Он ведет себя, как описано в разделе *переопределение свойства «DependsOn»* на [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Можно использовать **переместить** задач, а не **копирования** задач, если предпочтительный. Если выбран этот вариант и выполняется построение в Windows, необходимо использовать имя задачи полное `<Microsoft.Build.Tasks.Move>` во избежание неоднозначности XamarinVS задачи построения.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>В версиях до Xamarin Studio 6.0.0.5174 | Xamarin для Visual Studio 4.1.0.530

Да, для этого можно использовать настраиваемые целевые объекты MSBuild. Самый простой вариант — возможно, копирование `.ipa` файла после его построения.

Эти шаги будут работать для любого проекта iOS подсистемой сборки MSBuild на Mac и Windows. (Примечание: все проекты единой API используют ядро сборки MSBuild.)

1. Откройте `.csproj` в файле проекта приложения iOS в текстовом редакторе, а затем добавьте следующие строки в конец (непосредственно перед закрывающим тегом `</Project>` тега).

    ```csharp
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
        Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. Задать `DestinationFolder` к требуемой папке выходных данных. В обычном режиме может использовать свойства MSBuild (как `$(OutputPath)`) в рамках этого аргумента при необходимости.

## <a name="notes"></a>Примечания
- `CreateIpaDependsOn` Свойство определено в `Xamarin.iOS.Common.targets` файл, который является частью Xamarin.iOS. Он ведет себя, как описано в разделе *переопределение свойства «DependsOn»* на [ https://msdn.microsoft.com/library/ms366724.aspx ](https://msdn.microsoft.com/library/ms366724.aspx).

- Можно использовать **переместить** задач, а не **копирования** задач, если предпочтительный. Если выбран этот вариант и выполняется построение в Windows, необходимо использовать имя задачи полное `<Microsoft.Build.Tasks.Move>` во избежание неоднозначности XamarinVS задачи построения.
