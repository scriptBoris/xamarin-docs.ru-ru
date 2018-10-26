---
title: Работа со списками свойств в Xamarin.iOS
description: В этом документе представлены Visual Studio для Mac графический и расширенные свойства списка (.plist) редактор для работы с Info.plist и Entitlements.plist. Он иллюстрирует параметр значков и изображений при запуске для приложений iOS из среды Visual Studio для Mac.
ms.prod: xamarin
ms.assetid: 5E687043-0443-377C-9A12-9C5A05958646
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 7056f7beb623bee32c767a3f2827efa6eb2a6136
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118803"
---
# <a name="working-with-property-lists-in-xamarinios"></a>Работа со списками свойств в Xamarin.iOS

_В этом документе представлены Visual Studio для Mac графический и расширенные свойства списка (.plist) редактор для работы с Info.plist и Entitlements.plist. Он иллюстрирует параметр значков и изображений при запуске для приложений iOS из среды Visual Studio для Mac._

Visual Studio для Mac функции это plist-файл в графический редактор, который затрудняет внесение изменений в свойства приложения и возможности проще. Visual Studio для Mac имеет два .plists - `Info.plist` для редактирования свойств приложения и значки, и `Entitlements.plist` для управления функциями приложений. В этом руководстве представлены Info.plists и общие сведения о работе с ними в Visual Studio для Mac. Сведения о Entitlements.plist, см. в разделе [работа с назначениями](~/ios/deploy-test/provisioning/entitlements.md) руководства.

## <a name="infoplist"></a>Info.plist

В списке свойств сведения ( `Info.plist`) — файл требуется iOS, который предоставляет сведения о конфигурации вашего приложения в системе. Visual Studio для Mac пользовательских `Info.plist` функций редактора, три панели, контролируются вкладок в нижней левой части окна редактора:

 [![](property-lists-images/tabs.png "Вкладки редактора Info.plist в нижней левой части окна редактора")](property-lists-images/tabs.png#lightbox)

Каждой панели на различных свойств элементов управления, как показано ниже:

-  **Панель приложения** -графический интерфейс для установки общих свойств приложения, а также значки и запуска образов; укажите интеграция с картами и фоновая обработка режимов.
-  **Дополнительные панели** -«Дополнительно» — это место для указания типов поддерживаемых документов, Uti и типы URL-адресов.
-  **Панель источника** -панели «Источник» управляет менее общие свойства, а также пользовательские свойства для приложения.


Следующих трех разделах Изучите функции каждой панели, более подробно.

## <a name="application-panel"></a>Панель приложения

Графический интерфейс для редактирования общих функций Visual Studio для Mac `Info.plist` записи для приложения:

1.  Свойства приложения
1.  Типы поддерживаемых устройств
1.  Поддерживаемые ориентации для каждого типа устройств
1.  Строки стиль и цвет состояния
1.  Значки и экраны запуска
1.  Карты и фоновые режимы


Они описаны более подробно в следующих разделах.

 <a name="iOS_Application_Target" />


### <a name="ios-application-target"></a>Целевая платформа приложения iOS

Этот раздел содержит важные сведения, описывающие приложение.
**Идентификатор** хранятся здесь должен совпадать с идентификатором пакета, указанным в iTunes Connect (для приложений App Store), а также в списке подготовки идентификаторов приложений портала для iOS и разработки и распространения сертификатов.

 [![](property-lists-images/image24.png "Целевая платформа приложения iOS")](property-lists-images/image24.png#lightbox)

### <a name="device-deployment"></a>Развертывание устройств

 [![](property-lists-images/deployment.png "Развертывание устройств")](property-lists-images/deployment.png#lightbox)

Устройство **развертывания** выборочно, отображаются сведения о разделах в зависимости от выбора в **устройств** раскрывающийся список в **целевая платформа приложения** предыдущем разделе. **Главный интерфейс** раскрывающегося списка присваивается **MainStoryboard** в приложения на основе раскадровки. Если пользовательский интерфейс полностью написана в коде, то это может быть пустым.

### <a name="supported-device-orientations"></a>Поддерживаемые ориентации устройства

 **Поддерживаемые ориентации устройства** контролирует, как приложение реагирует на поворот экрана. Очень часто для iPhone и iPad приложений для поддержки только **книжной**, или все, но **сверху вниз**. Обычно все приложения iPad, кроме игры должны поддерживать все ориентации.

### <a name="status-bar-styles"></a>Стили строки состояния

**Стили полосы состояния** раздел — это графический интерфейс для редактирования приложения `UIStatusBarStyle`:

 [![](property-lists-images/status.png "Стили строки состояния")](property-lists-images/status.png#lightbox)

 <a name="Icons" />


### <a name="icons-launch-images-and-itunes-artwork"></a>Значки, изображения при запуске и иллюстрации iTunes

Сведения об использовании значки, изображения и иллюстрации в файле Info.plist можно найти в [работа с образами](~/ios/app-fundamentals/images-icons/index.md) руководства.




### <a name="maps-integration-and-background-modes"></a>Интеграция с картами и фоновые режимы

`Info.plist` Содержит специальные разделы для указания интеграция с картами и фоновая обработка режимов. Выбрав параметры, которые требуется поддерживать добавит необходимые свойства для вашего приложения за вас.

 [![](property-lists-images/maps.png "Интеграция с картами")](property-lists-images/maps.png#lightbox)

Дополнительные сведения о работе с картами, см. Xamarin [iOS Maps](~/ios/user-interface/controls/ios-maps/index.md) руководства.

 [![](property-lists-images/bging.png "Фоновые режимы")](property-lists-images/bging.png#lightbox)

Дополнительные сведения о фоновых режимов см. Xamarin [фоновый режим в iOS](~/ios/app-fundamentals/backgrounding/introduction-to-backgrounding-in-ios.md) руководства.

## <a name="advanced-panel"></a>«Дополнительно»

«Дополнительно» для управления типов документов и схемы URL-адресов, которые поддерживает приложение.

 [![](property-lists-images/image34.png "«Дополнительно»")](property-lists-images/image34.png#lightbox)

 <a name="Document_Types" />


## <a name="document-types"></a>Типы документов

Для приложений, которые поддерживают открытие файлов определенных типов, предоставляет iOS `CFBundleDocumentTypes` ключ. Если мы хотим наше приложение для поддержки определенных известных типов файлов — например, PDF - мы бы добавили значение PDF на ключ. В этом разделе предоставляет удобный способ для ввода данных, которая будет храниться в `CFBundleDocumentTypes` в `Info.plist` файл.

Обратитесь к документации на [Регистрация типов файлов, Your поддерживает приложения](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html) Дополнительные сведения о настройке этих значений.

## <a name="utis"></a>Uti

Иногда необходимо приложение поддерживает открытие пользовательского типа файлов. Например, можно открыть файлы изображений с помощью пользовательского расширения *.xam*. Для указания пользовательского типа файлов, мы создадим пользовательский UTI - универсальный идентификатор типа — с помощью `UIExportedTypeDeclarations` ключ. На следующем снимке экрана показано, как создать пользовательские UTI для расширения .xam:

 [![](property-lists-images/uti.png "Редактор Uti")](property-lists-images/uti.png#lightbox)

Как только что экспортированный Uti типов укажите пользовательские Uti определенному приложению, *импортированные Uti типов* ( `UIImportedTypeDeclarations` ключ) укажите пользовательские типы поддерживаются, но не принадлежит приложения.

Дополнительные сведения об использовании пользовательских Uti см Apple [регистрации файла типы Your поддерживает приложения](https://developer.apple.com/library/ios/documentation/FileManagement/Conceptual/understanding_utis/understand_utis_declare/understand_utis_declare.html#//apple_ref/doc/uid/TP40001319-CH204-SW1) руководства.

## <a name="custom-urls"></a>Пользовательские URL-адреса

Имя схемы URL-адрес (также называется "протокол") — это первая часть URL-адрес. Например `http://` и `https://` общих схем URL-адрес. У вас есть возможность создать схему URL-адрес для приложения. Пользовательские схемы URL-адреса используются для взаимодействия и передачи данных туда и обратно с другими приложениями. Снимке экрана ниже показано создание нового URL-адрес схему вызывается `monkeys://`:

 [![](property-lists-images/url.png "Пользовательские URL-адреса")](property-lists-images/url.png#lightbox)



Дополнительные сведения о реализации пользовательских схем URL-адреса см. Apple [реализации настраиваемых схем URL-адреса разделе данного руководства](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)

## <a name="source-panel"></a>«Источник»

**Источника** вкладке `Info.plist` файл позволяет пользовательские значения добавить или изменить. Visual Studio для Mac предоставляет список наиболее распространенных свойств:

 [![](property-lists-images/image31.png "Добавление нового свойства из раскрывающегося списка")](property-lists-images/image31.png#lightbox)

Для известных свойств Visual Studio для Mac будет список допустимых значений, как показано на следующем снимке экрана:

 [![](property-lists-images/image32.png "Выберите значение из списка известных значений")](property-lists-images/image32.png#lightbox)

Visual Studio для Mac также определяет тип свойства, как показано:

 [![](property-lists-images/image33.png "Доступные типы свойств")](property-lists-images/image33.png#lightbox)

Просмотрите Apple [связанные ресурсы приложения](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html) ссылки на дополнительные сведения о необязательных свойств.

 <a name="Entitlements" />

## <a name="summary"></a>Сводка

В этой статье демонстрируется использование редакторов графический и расширенные plist-файл для редактирования стандартных конфигураций приложений также указать значки и изображения при запуске. Он также появился `Entitlements.plist` для добавления и управления ими возможности приложений.


## <a name="related-links"></a>Связанные ссылки

- [ИНТЕГРИРОВАННАЯ СРЕДА РАЗРАБОТКИ](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/ide)
- [Приложения, связанные с ней ресурсы](http://developer.apple.com/library/ios/#DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html)
- [Регистрация файла типы поддерживает ваше приложение](http://developer.apple.com/library/ios/#documentation/FileManagement/Conceptual/DocumentInteraction_TopicsForIOS/Articles/RegisteringtheFileTypesYourAppSupports.html)
- [Реализация схемы пользовательский URL-адрес](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/AdvancedAppTricks/AdvancedAppTricks.html)
- [Справочнике по формату каталог активов](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_ref-Asset_Catalog_Format/index.html#//apple_ref/doc/uid/TP40015170-CH18-SW1)
