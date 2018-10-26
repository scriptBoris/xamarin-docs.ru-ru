---
title: Настраиваемый документ значки в Xamarin.iOS
description: Этот статье охватывает, включая и управление ими ресурс изображения в приложение Xamarin.iOS для использования в качестве пользовательский значок типа документа.
ms.prod: xamarin
ms.assetid: 7A3F3C94-2578-4F53-9B8E-25714F48BDD6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/23/2017
ms.openlocfilehash: 51abd00f9a21b702811bb3897f273deff54f7d01
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110489"
---
# <a name="custom-document-icons-in-xamarinios"></a>Настраиваемый документ значки в Xamarin.iOS

_Этот статье охватывает, включая и управление ими ресурс изображения в приложение Xamarin.iOS для использования в качестве пользовательский значок типа документа._

Если приложения Xamarin.iOS поддерживает загрузку определенного типа документов, разработчик может предоставить значков, которые будут использоваться при обнаружении этого типа документа, например когда пользователь удерживает вложения в *почтовое приложение* как Вот как:

 [![](custom-document-types-images/17.png "Пример значки для типов документов")](custom-document-types-images/17.png#lightbox)

Разработчик может добавить сведения о типе документа в формате файла приложения — для открытия, включив словарей для `CFBundleTypeName` строку и `LSItemContentTypes` массива в приложении `Info.plist`. Значки для типа документа перейдите `CFBundleTypeIconFiles` массива. Если значок документа не указано, iOS будет наследовать из значка приложения.
Значки могут быть предоставлены для несколько размеров, оптимизированные для различных разрешений устройства. 

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Чтобы назначить эти значения в Visual Studio для Mac, используйте **типов документов** раздел **Дополнительно** вкладке `Info.plist` редактор, чтобы добавить тип документа и назначить ей изображения значков. Например ниже приведен снимок экрана, показывающий регистрации для поддержки PDF.

 [![](custom-document-types-images/18.png "В разделе типов документов, в разделе вкладка \"Дополнительно\" в редакторе «Info.plist»")](custom-document-types-images/18.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Чтобы назначить эти значения в Visual Studio, используйте **типов документов** раздел **Дополнительно** вкладке `Info.plist`:

 ![](custom-document-types-images/doc01w.png "Откройте раздел типов документов в разделе вкладка \"Дополнительно\"")

Нажмите кнопку **добавить тип документа** кнопку и заполните обязательные поля:

![](custom-document-types-images/doc02w.png "Форма добавления типа документа")

-----


Дополнительные сведения о типах документов см. в разделе Apple [универсальный ссылка на тип идентификаторы](http://developer.apple.com/library/ios/#documentation/Miscellaneous/Reference/UTIRef/Articles/System-DeclaredUniformTypeIdentifiers.html) и [разделы программирования документа взаимодействия для iOS](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Introduction/Introduction.html).


## <a name="related-links"></a>Связанные ссылки

- [Работа с образами (пример)](https://developer.xamarin.com/samples/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Пользовательский значок и правила создания образа](http://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
