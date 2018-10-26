---
title: 'Ошибка IBTool: Не удалось завершить операцию.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 9458c1f8efa36199b21127123f0013b494a224b6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103742"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Ошибка IBTool: Не удалось завершить операцию.

## <a name="fixed-in-xcode-611"></a>Исправлена в Xcode 6.1.1

Apple [фиксированной](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) это `ibtool` об ошибке в Xcode 6.1.1, поэтому обновление до Xcode 6.1.1 или более поздней версии — самый простой исправления.

* * *

## <a name="description-of-the-problem"></a>Описание проблемы

`ibtool` Команды в Xcode 6.0 возникала ошибка на OS X 10.10 Yosemite. Xamarin.iOS использует Xcode `ibtool` для компиляции раскадровки и `XIB` файлы.

Дополнительные сведения об ошибке по отношению к Xcode можно найти по приведенным ниже запись Stack Overflow: [http://stackoverflow.com/questions/25754763/cant-open-storyboard](http://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Сообщение об ошибке

> Не удалось открыть документ «MainStoryboard.storyboard». Не удалось завершить операцию. (ошибка com.apple.InterfaceBuilder -1.)

## <a name="workarounds-for-xcode-60"></a>Обходные пути (для Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Вариант 1: Управлять всеми `UIImageView.Image` свойства в коде

Вместо установки `Image` свойство `UIImageView` в раскадровке или `.xib` файл, можно задать свойства в одном из представления жизненного цикла переопределение методы в контроллере представления (например, в `ViewDidLoad()`). См. также [работа с образами](~/ios/app-fundamentals/images-icons/index.md) советы по использованию `UIImage.FromBundle()` и `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Вариант 2: Переместить все графические ресурсы верхнего уровня `Resources` папки

После перемещения изображений высшего уровня `Resources` папку, необходимо будет обновить раскадровку и `.xib` файлы для использования нового пути к изображениям.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Вариант 3: Задайте `LogicalName` для любого проблемных графические активы, поэтому их копирования на верхнем уровне`.app` пакета

Предположим, например, первоначальный `.csproj` файл содержит следующую запись:

`<BundleResource Include="Resources\Images\image.png" />`

Можно изменить этот элемент и добавьте `LogicalName` таким образом, чтобы вместо этого изображение будет скопировано на верхнем уровне `.app `пакета:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

В Visual Studio для Mac `LogicalName` также можно задать с помощью `Resource ID` поле для изображения в разделе **представление > панели > свойства**. (См. также: [ http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545 ](http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

После этого изменения необходимо обновить, раскадровки и `.xib` файлы, чтобы использовать новые пути верхнего уровня образа. Visual Studio для Mac автоматически обновит список autocompletions для `Image` свойства в конструкторе iOS. В Visual Studio вам потребуется вручную изменить путь. Конструктор iOS, будет выведено это как образ отсутствует, но проекта построения и запуска правильно.

### <a name="next-steps"></a>Следующие шаги

Дополнительная помощь, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения, ознакомьтесь с разделом [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах связи, предложения, а также как файл новую ошибку, при необходимости. 

