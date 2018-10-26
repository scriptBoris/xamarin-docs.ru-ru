---
title: Копирование выходных IPA-файлов в папку сброса TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 74e2f2219dcb0908edce7f109844932639038b25
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113044"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Копирование выходных IPA-файлов в папку сброса TFS?

Откройте `.csproj` файл для проекта приложения iOS в текстовом редакторе и затем добавьте следующие строки в конце (непосредственно перед закрывающим `</Project>` тега):

```xml
<PropertyGroup>
    <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
    </CreateIpaDependsOn>
</PropertyGroup>

<Target Name="CopyIpa"
    Condition="'$(OutputType)' == 'Exe'
        And '$(ComputedPlatform)' == 'iPhone'
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Примечания

- Это же общего метода, описанного на [можно изменить путь к выходной файл IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). Два важных аспекта, чтобы задать `$(TF_BUILD_BINARIESDIRECTORY)` конечной папке и чтобы добавить дополнительное условие, `CopyIpa` будет выполняться только для сборок TFS.

- Описание `TF_BUILD_BINARIESDIRECTORY` см. в разделе [предварительно определенные переменные сборки](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Дополнительные ссылки

- [Документация по установке TFS для использования с Xamarin](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Задача сборки Azure DevOps: Xamarin.Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Задача сборки Azure DevOps: Xamarin.iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Следующие шаги

В этом документе рассматриваются текущее поведение начиная с Xamarin 3.11.666 для Visual Studio и узла сборки Xamarin.iOS 8.10.3 на компьютере Mac. Дополнительная помощь, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения, ознакомьтесь с разделом [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах связи, предложения, а также как файл новую ошибку, при необходимости.
