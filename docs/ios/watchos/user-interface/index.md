---
title: элементы управления пользовательского интерфейса в Xamarin watchOS
description: В этом документе описываются различные элементы управления, доступные для использования в пользовательском интерфейсе watchOS. Он содержит описание метки, кнопки, переключатели, ползунки, изображения, разделители, карты и многое другое.
ms.prod: xamarin
ms.assetid: EDFAD203-02EA-4A74-9CE2-7B8513BC90E1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/19/2016
ms.openlocfilehash: c8ef76f24b017f5e3e6bec9d39534f3626e79147
ms.sourcegitcommit: 2713f2c1d74e3582704c3d0ca65b6651119ed489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56321107"
---
# <a name="watchos-user-interface-controls-in-xamarin"></a>элементы управления пользовательского интерфейса в Xamarin watchOS

[ **WatchKitCatalog** ](https://github.com/xamarin/monotouch-samples/tree/master/watchOS/WatchKitCatalog) демонстрирует различные элементы управления watchOS. Раскадровки приложения показан здесь (щелкните, чтобы увеличить):

[![](images/storyboard-sml.png "Пример макета watchOS")](images/storyboard.png#lightbox)

Программный имена всех элементов управления будет начинаться с префикса `WKInterface` (например) `WKInterfaceLabel`, `WKInterfaceButton`).

|Элемент управления|Описание:|Снимок экрана|
|---|---|---|
|Метка|Используйте `SetText` и другие свойства, определяющие внешний вид текста в элементе управления label. `NSAttributedString` также поддерживается.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/LabelDetailController.cs)|![](Images/label.png)|
|Кнопка|Создайте и задайте свойства в раскадровке. CTRL + перетащите, чтобы добавить `Action` реализовать обработчик для при его выборе.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ButtonDetailController.cs)|![](Images/button.png)|
|Параметр|Используйте `SetOn` для управления состоянием коммутатора.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SwitchDetailController.cs)|![](Images/switch.png)|
|Slider|Возможны множество различных стилей.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SliderDetailController.cs)|![](Images/slider.png)|
|Изображение|Используйте `myImage.SetImage("MyWatchImage")` для загрузки изображений в watch, или `WKInterfaceDevice.CurrentDevice.AddCachedImage` кэшировать их для повторного использования в watch.<br />[Документацию по контролю образа](~/ios/watchos/user-interface/image.md)<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ImageDetailController.cs)|![](Images/image.png)|
|Separator|Разделители используются для создания привлекательных watch пользовательских интерфейсов.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/SeparatorDetailController.cs)|![](Images/separator.png)| 
|Карта|Изображение карты статически отображается в watch, однако вы можете контролировать многие аспекты его представления, включая добавление ПИН-кодов.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MapDetailController.cs)|![](Images/map.png)|
|Movie & InlineMove|Фильмы, могут открыть сами по себе или встроенная<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/MovieDetailController.cs)|![](Images/movie.png)|
|Группа|Группы можно используйте для создания привлекательных watch пользовательских интерфейсов.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GroupDetailController.cs)|![](Images/group.png)|
|Таблица|Упрощенную версию таблиц на iOS. Реализуйте `DidSelectRow` реакция на выбор пользователя (или используйте segue).<br />[Документацию по контролю таблицы](~/ios/watchos/user-interface/table.md)<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/Table%20Detail%20Controller/TableDetailController.cs)|![](Images/table.png)|
|Устройство|`WKInterfaceDevice.CurrentDevice` содержит свойства, такие как `ScreenBounds`, `ScreenScale`, и `PreferredContentSizeCategory`.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/DeviceDetailController.cs)|![](Images/device.png)|
|[Menu](~/ios/watchos/user-interface/menu.md)|Определить меню нажмите клавишу force в раскадровке и реализовать действия для каждой кнопки в коде.<br />[Документация по меню (Force Touch) элемента управления](~/ios/watchos/user-interface/menu.md)<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/ControllerDetailController.cs)|![](Images/controller.png)|
|Текстовый ввод|Используйте `PresentTextInputController` и `WKTextInputMode` перечисления.<br />[Документация по текстовой входных данных](~/ios/watchos/user-interface/text-input.md)<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/TextInputController.cs)|![](Images/textinput.png)|
|Цифровой корона|Цифровой корона может использоваться для диска управляющий элемент выбора или в коде можно отслеживать его поворота.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/CrownDetailController.cs)|![](Images/digital-crown.png)|
|Жесты|Существует четыре типа распознавания жестов, которые могут добавляться в сцену. TAP, проведите по экрану, сдвига и LongPress.<br />[Код каталога](https://github.com/xamarin/ios-samples/blob/master/watchOS/WatchKitCatalog/WatchKit3Extension/GestureDetailController.cs)|![](Images/gestures.png)|


## <a name="related-links"></a>Связанные ссылки

- [WatchKitCatalog (пример)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Справочник по API пакета контрольных значений](xref:WatchKit)
