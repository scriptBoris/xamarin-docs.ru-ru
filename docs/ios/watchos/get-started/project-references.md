---
title: ссылки на проект в Xamarin watchOS
description: В этом документе описывается связь между приложением iOS, приложение просмотра и расширение просмотра приложения. В нем описывается ссылки проекта и пакета идентификаторы.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c900ab714fed2bb1e02367ba39ad3c5a0a76121e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106667"
---
# <a name="watchos-project-references-in-xamarin"></a>ссылки на проект в Xamarin watchOS

_Объяснение связь между приложением iOS, приложение просмотра и расширение просмотра._

Трех проектов в решении watchOS *автоматически настроенного* ссылаться друг с другом определенным образом для приложений watchOS 3 в их создании и объединенными правильно. Эти ссылки проекта и параметров идентификатора пакета описаны ниже для справки.

## <a name="project-references"></a>Ссылки проекта

Просмотрите ссылки, дважды щелкнув ссылки на узлы для каждого проекта:

- **приложение для iPhone** ссылки **приложении для Apple Watch**

![](project-references-images/catalog-reference1.png "приложение для iPhone, ссылается на приложения Watch")

- **Просмотрите приложения** ссылки **расширение приложения Watch**

![](project-references-images/catalog-reference2.png "приложение для iPhone, ссылается на приложения Watch")


 - **Расширение приложения Watch** не ссылается ни один из других проектов

![](project-references-images/catalog-reference3.png "Расширение приложения Watch не ссылается на другие проекты")



## <a name="bundle-identifiers"></a>Идентификаторы пакета

Необходимо также убедитесь, что ваш **идентификаторов пакета** указаны правильно.
Все три проекта должен иметь *же* префикс идентификатора, с проектами две контрольные значения, предопределенные расширения `watchkitextension` и `watchkitapp`, как показано ниже (для **WatchKitCatalog** пример).

 - Проект единого Xamarin.iOS: `com.xamarin.WatchKitCatalog`

 - Проект расширения WatchKit- `com.xamarin.WatchKitCatalog.watchkitextension`

 - Проект приложения Watch- `com.xamarin.WatchKitCatalog.watchkitapp`

Также убедитесь, что они **Info.plist** параметры настроены правильно:

 - В проект приложения Watch `WKCompanionAppBundleIdentifier` совпадает идентификатор пакета приложения родительского или контейнера (т. е., запущенным на iPhone);

 - Расширение пакета средств просмотра проекта **идентификатор пакета WKApp** совпадает идентификатор пакета в проект приложения Watch.

Идентификаторы можно изменить, дважды щелкнув **Info.plist** файла в каждом проекте.

На этом снимке экрана — **расширение просмотра** фрагмент файла Info.plist **приложение Watch** также идентификатор:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)
    
![](project-references-images/infoplist-extension.png "На этом снимке экрана — это файл Info.plist расширения контрольных значений")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
![](project-references-images/infoplist-extension-vs.png "На этом снимке экрана — это файл Info.plist расширения контрольных значений")

-----

На этом снимке экрана — **приложение Watch** файл Info.plist.
Текущий **Watch OS** версия 8.2, поэтому **цель развертывания** должно находиться в приложении для Apple Watch **8.2**. Обратите внимание, что если у вас установлен 6.3 Xcode, это значение может быть присвоено 8.3 его следует менять 8.2.

![](project-references-images/infoplist-watchapp.png "Просмотрите файл Info.plist")

Целевой объект развертывания для приложения Watch может отличаться от Watch расширение и приложение iOS.

