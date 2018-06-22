---
title: 'Ошибка IBTool: Не удалось завершить операцию.'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: A804EBC4-2BBF-4A98-A4E8-A455DB2E8A17
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 4647227ad208bfa968f8282a966220a09ab7f4a6
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30777155"
---
# <a name="ibtool-error-the-operation-couldnt-be-completed"></a>Ошибка IBTool: Не удалось завершить операцию.

## <a name="fixed-in-xcode-611"></a>Исправлено в Xcode 6.1.1

Apple [фиксированной](https://developer.apple.com/library/content/documentation/Xcode/Conceptual/RN-Xcode-Archive/Chapters/xc6_release_notes.html#//apple_ref/doc/uid/TP40016994-CH4-SW1) это `ibtool` простой исправление является ошибка в Xcode 6.1.1, поэтому обновление для Xcode 6.1.1 или более поздней версии.

* * *

## <a name="description-of-the-problem"></a>Описание проблемы

`ibtool` Команды в Xcode 6.0 бы ошибку в OS X 10.10 Yosemite. В Xcode использует Xamarin.iOS `ibtool` для компиляции раскадровки и `XIB` файлов.

Дополнительные сведения об ошибке, относительно Xcode можно найти на следующих переполнения стека post: [http://stackoverflow.com/questions/25754763/cant-open-storyboard](http://stackoverflow.com/questions/25754763/cant-open-storyboard)

### <a name="error-message"></a>Сообщение об ошибке

> Не удалось открыть документ «MainStoryboard.storyboard». Не удалось завершить операцию. (ошибка com.apple.InterfaceBuilder -1.)

## <a name="workarounds-for-xcode-60"></a>Возможные решения (Xcode 6.0)

### <a name="option-1-manage-all-uiimageviewimage-properties-in-code"></a>Вариант 1: Управление всеми `UIImageView.Image` свойствам в коде

Вместо установки `Image` свойство `UIImageView` в раскадровку или `.xib` файла, задается в одном представлении переопределенные методы жизненного цикла в контроллере представления (например, в `ViewDidLoad()`). См. также [работа с образами](~/ios/app-fundamentals/images-icons/index.md) советы по использованию `UIImage.FromBundle()` и `UIImage.FromFile()`.

### <a name="option-2-move-all-of-the-image-resources-to-the-top-level-resources-folder"></a>Вариант 2: Переместите все графические ресурсы верхнего уровня `Resources` папки

После перемещения изображений высшего уровня `Resources` папки, будет необходимо обновить раскадровки и `.xib` файлы для использования нового пути к изображению.

### <a name="option-3-set-the-logicalname-for-any-problematic-image-assets-so-they-are-copied-to-the-top-level-of-theapp-bundle"></a>Вариант 3: Задайте `LogicalName` для любого проблемный графические активы, они копируются на верхнем уровне`.app` пакета

Например, первоначальный `.csproj` файл содержит следующую запись:

`<BundleResource Include="Resources\Images\image.png" />`

Можно изменить этот элемент и добавить `LogicalName` , чтобы вместо этого изображение будет скопировано на верхнем уровне `.app `пакета:

```xml
<BundleResource Include="Resources\Images\image.png">
    <LogicalName>image.png</LogicalName>
</BundleResource>
```

В Visual Studio для Mac `LogicalName` можно также задать с помощью `Resource ID` для образа в разделе **представление > дополняет > свойства**. (См. также: [ http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545 ](http://stackoverflow.com/questions/16938250/xamarin-studio-folder-structure-issue-in-ios-project/16951545#16951545))

После этого изменения будет необходимо обновить раскадровки и `.xib` файлы для использования нового пути образа верхнего уровня. Visual Studio для Mac автоматически обновит список autocompletions для `Image` свойства в конструкторе iOS. В Visual Studio необходимо будет вручную изменить путь. Конструктор iOS, будет выведено это как образ отсутствует, но проект построения и запуска правильно.

### <a name="next-steps"></a>Следующие шаги

Для получения дополнительной помощи, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения см. в разделе [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах контактов, предложения, а также как файл новую ошибку, при необходимости. 

