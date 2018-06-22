---
title: Как скопировать выходные файлы IPA транзитный каталог TFS?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 087a20ea3b573595e6cbd2b40d77de649676391e
ms.sourcegitcommit: dc882e9631b4ed52596b944a6fbbdde309346943
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2018
ms.locfileid: "31883713"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Как скопировать выходные файлы IPA транзитный каталог TFS?

Откройте `.csproj` в файле проекта приложения iOS в текстовом редакторе, а затем добавьте следующие строки в конец (непосредственно перед закрывающим тегом `</Project>` тега):

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

-   Это же общего метода, описанного в [можно изменить путь выходного файла IPA?](~/ios/troubleshooting/questions/ipa-output-path.md). Два важных аспекта, чтобы задать `$(TF_BUILD_BINARIESDIRECTORY)` качестве конечной папки и добавлять дополнительное условие так `CopyIpa` будет выполняться только для сборок TFS.

-   Описание `TF_BUILD_BINARIESDIRECTORY` разделе [ https://msdn.microsoft.com/library/hh850448.aspx ](https://msdn.microsoft.com/library/hh850448.aspx).

## <a name="additional-references"></a>Дополнительные ссылки

- [Документация по установке TFS для использования с Xamarin](https://docs.microsoft.com/vsts/tfvc/overview)
- [Задача сборки TFS: Xamarin.Android](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-android)
- [Задача сборки TFS: Xamarin.iOS](https://docs.microsoft.com/vsts/build-release/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Следующие шаги
В этом документе рассматриваются текущее поведение начиная с Xamarin 3.11.666 для Visual Studio и создавайте Xamarin.iOS 8.10.3 на MAC-адрес узла. Для получения дополнительной помощи, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения см. в разделе [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах контактов, предложения, а также как файл новую ошибку, при необходимости. 



