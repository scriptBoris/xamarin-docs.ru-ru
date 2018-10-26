---
title: Изменения платформы дополнительных tvOS 10
description: В этом документе описываются незначительные изменения и усовершенствования, внесенные в имеющиеся платформы в iOS 10. Здесь рассматриваются обновления AVFoundation, AVKit, Core Data, Core Graphics, Foundation, GameKit, GameplayKit и многое другое.
ms.prod: xamarin
ms.assetid: F771640A-F92E-4954-82D5-2D720434971E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 8f7094cebf284f301e08faae8d94c99a73964d55
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114292"
---
# <a name="additional-tvos-10-frameworks-changes"></a>Изменения платформы дополнительных tvOS 10

Помимо основных изменения tvOS Apple сделала изменения и усовершенствования для нескольких существующих платформ в tvOS 10.

<a name="AV-Foundation-Framework" />

## <a name="avfoundation-framework-additions"></a>Дополнения AVFoundation Framework

Платформа AVFoundation включает следующие улучшения:

 - В tvOS 10, приложение больше не реализует различные [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) поведения на основе типа содержимого. Просто задайте `Rate` свойство и AVFoundation определяют при достаточно содержимое доступно для воспроизведения без замедляется работа.
 - Новый `AVPlayerLooper` класс упрощает цикл данной части носителей во время воспроизведения.

<a name="AVKit-Framework-Enhancements" />

## <a name="avkit-framework-enhancements"></a>Усовершенствования AVKit Framework

Платформа AVKit включает следующие улучшения:

 - Приложение теперь имеет контроль над пропуск поведение [AVPlayerViewController](https://developer.apple.com/reference/avkit/avplayerviewcontroller) пропуск жест может перемещать к следующему элементу в список воспроизведения или advance внутри текущего элемента.

<a name="Core-Data-Enhancements" />

## <a name="core-data-enhancements"></a>Усовершенствования основных данных

tvOS 10 включает следующие улучшения в Framework Core Data:

 - Корневой [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) объектов поддерживает параллельные завершение со сбоем и выборка без сериализации.
 - [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) класс поддерживается пул хранилищ данных SQLite.
 - [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) объектов с хранилищами данных SQLite в режиме журнал WAL поддержки нового поколения запроса компонентов, где управляемый объект контекстов (MOC) можно прикрепить к версии конкретной базы данных для будущих выборки и Ошибка транзакции.
 - С помощью высокоуровневым `NSPersistenceContainer` ссылка `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) и другим ресурсам конфигурации Core Data.
 - Были добавлены несколько новых удобных методов `NSManagedObject` облегчая процесс для выполнения операций выборки и создание подклассов.

Дополнительные сведения см. в разделе Apple [справочные материалы по основной данных Framework](https://developer.apple.com/reference/coredata).

<a name="Core-Graphics-Enhancements" />

## <a name="core-graphics-enhancements"></a>Главные усовершенствования графики

tvOS 10 включает следующие улучшения в Core Framework графики:

 - Новый [CGColorConverterRef](https://developer.apple.com/reference/coregraphics/cgcolorconverterref) класс может использоваться для выполнения ряда преобразования цветов.

<a name="Core-Image-Enhancements" />

## <a name="core-image-enhancements"></a>Главные усовершенствования образа

tvOS 10 вносит следующие усовершенствования framework образ основных компонентов:

 - `ImageWithExtent` Метод [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) класс может использоваться для вставки пользовательской обработки в операцию фильтрации. Основной образ будет вызывать данным обратным вызовом от фильтров при обработке изображения для выходных данных или отображения.
 - Приложение теперь может обрабатывать образы в цветовом пространстве вне контекста образ основных компонентов рабочее цветовое пространство путем преобразования в и из него цветовое пространство до и после обработки.
 - Были внесены некоторые улучшения производительности отрисовки `UIImage` визуализации (когда хранилищ образа образ основных компонентов) в `UIImageView` объектов. 
 - `UIImage` объекты с тегами wide-цветовой гаммы визуализирует цвета в цветовой гаммы wide в `UIImageView` объекты на устройствах iOS, поддерживающих широкая цветовая палитра.
 - Основной образ ядра код теперь можете запросить определенный пиксель выходных форматов.

Кроме того были добавлены следующие новые фильтры образ основных компонентов:

 - `CINinePartTiled`
 - `CINinePartStretched`
 - `CIHueSaturationValueGradient`
 - `CIEdgePreserveUpsampleFilter`
 - `CIClamp`

<a name="Foundation-Enhancements" />

## <a name="foundation-enhancements"></a>Усовершенствования Foundation

В среде Foundation для tvOS 10 были внесены следующие улучшения:

 - Используйте новый [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) класса, чтобы дата и время вычисления интервала, например длительности для сравнения интервалы и тестирование для пересечения интервал.
 - Были добавлены несколько новых свойств [NSLocal](https://developer.apple.com/reference/foundation/nslocale) класс получения местной информации и отображение доступных форматов.
 - Используйте новый [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) класс для преобразования между разные единицы из мер (единица Измерения) или выполняют вычисления над значениями в разных UOMs.
 - Используйте новый [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) класс для форматирования локализованного измерения для отображения для конечного пользователя.
 - Используйте новый [NSUnit](https://developer.apple.com/reference/foundation/nsunit) и [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) классы для представления определенных UOMs.

<a name="GameKit-Enhancements" />

## <a name="gamekit-enhancements"></a>Усовершенствования GameKit

Платформа "GameKit" в tvOS 10 были внесены следующие улучшения:

 - Реализован новый тип учетную запись iCloud [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) класса.
 - Новый [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) класс предоставляет универсального решения для управления хранилищем постоянных данных в Game Center. `GKGameSession` хранит список игроков и приложение является ответственным формы реализации, как и когда участника дата хранится, извлечь или обмениваются игроков. Во многих случаях игр сеансов можно заменить существующих Поочередный совпадений, совпадений в режиме реального времени или постоянных игры сохранить методы.

<a name="GameplayKit-Enhancements" />

## <a name="gameplaykit-enhancements"></a>Усовершенствования GameplayKit

Платформа GameplayKit в tvOS 10 были внесены следующие улучшения:

 - Поколение процедурного пропускаемые была добавлена и может использоваться для повышения реалистичности в выглядящие естественно текстуры, добавить реализм перемещение камеры и помогают создавать полнофункциональной игровой среды.
 - Используйте Пространственные секционирования для секционирования данных игровой для эффективного поиска.
 - Специалист по стратегиям новый методом Монте-Карло ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) был добавлен для вычислений исчерпывающим возможных перемещения.
 - Добавлен новый интерфейс API дерева принятия решений ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) и [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) для улучшения AI игры дом.
 - Поддержка трехмерного представления был добавлен существующий агент и поведения путь поиска, с помощью нового [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) и [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) классы.
 - Используйте новый [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) класса для обеспечения высокой производительности — это Естественный пути.
 - Новый [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) и [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) классы марки, объединение GameplayKit и проще, чем когда-либо SpriteKit.

<a name="Metal-Enhancements" />

## <a name="metal-enhancements"></a>Усовершенствования исходного состояния системы

К платформе без операционной системы в tvOS 10 были внесены следующие улучшения:

 - Теперь можно использовать трехмерных приложениях и играх _тесселяции_ для эффективного отображения сложных сцен и geometry с помощью графического Процессора.
 - Используйте специализацию функции для создания коллекции оптимизирован материалов и света сочетания функций для сцены.
 - Предоставлять детальный контроль выделения ресурсов для оптимизации работы операционной системы на основе приложений с помощью ресурсов кучи и Memoryless целевых буферов визуализации.

Дополнительные сведения см. в разделе Apple [руководство по программированию без операционной системы](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="Metal-Performance-Shaders-Enhancements" />

## <a name="metal-performance-shaders-enhancements"></a>Шейдеры усовершенствования производительности без операционной системы

Платформа производительности шейдеров без операционной системы в tvOS 10 были внесены следующие улучшения:

 - Были добавлены многие новых ядер framework шейдеры производительности без операционной системы, чтобы разрешить приложению использовать преимущества вычислений оптимизированного для высокой, параллельной обработки данных, например преобразования цветов пространство и операции нейронной сети.

<a name="ModelIO-Enhancements" />

## <a name="modelio-enhancements"></a>Усовершенствования ModelIO

Платформа ModelIO в tvOS 10 были внесены следующие улучшения:

 - Теперь поддерживается формат файла долл. США.
 - Используйте новый `MDLMaterialPropertyGraph` класса для упрощения поддержки среды выполнения изменения в модели.
 - Подпись поля расстояние, добавлена поддержка [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) класса.
 - Используйте новый `MDLLightProbeIrradianceDataSource` класс, помогающий в свет выборки данных размещения.

<a name="SceneKit-Enhancements" />

## <a name="scenekit-enhancements"></a>Усовершенствования SceneKit

Платформа SceneKit в tvOS 10 были внесены следующие улучшения:

 - SceneKit теперь включает в себя новую систему физически основе визуализации (PBR) для более реалистичного результатов разработки, более простой ресурс.
 - Используйте новый [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) заливки модель, в которой продукта широкий спектр реалистичную заливку эффекты при этом не только для трех основных свойств (`Diffuse`, `Metalness` и `Roughness`).
 - С момента PBR заливки работает лучше всего с освещением, основанная на среде, используйте `LightingEnvironment` свойство, чтобы назначить освещение на основе образа для tan всей сцены.
 - Используйте `IESProfileURL` свойство для импорта приборами реального мира, которые определяют базовый освещения на реальные значения, такие как интенсивность (в люмен) и цветовой температуры (в градусах Кельвина).
 - [SCNCamera](https://developer.apple.com/reference/scenekit/scncamera) класс может предоставить больше реализм с помощью функций HDR и эффекты. Используйте адаптивной раскрытия для создания автоматического эффекты или виньетирование использования, каймы цвет и цвет градации Добавление filmatic эффекты для игры.
 - PBR и HDR камеры функции обеспечивают лучшие результаты, чем методы традиционных отрисовки и, таким образом, SceneKit теперь выполняет все вычисления цвета в линейном цветовом пространстве (с использованием цветового P3 дисплеям устройств wide цвет).
 - SceneKit теперь цвет соответствует все цвета, считывая данные профиля цвета.
 - SceneKit интерпретирует значений составляющих цвета в линейной цветовое пространство RGB для всех типов шейдера.
 - Поскольку SceneKit считывает и настроить сведения о профиле цвет в изображения текстур, используйте каталоги активов для всех образов, чтобы убедиться, что эта информация.
 - Линейные цветовые пространства отрисовки и расширенных цвет можно отключить, задав `SCNDisableLinearSpaceRendering` и `SCNDisableWideGamut` ключей в приложении `Info.plist`.
 - Создавать произвольные многоугольника приматов (либо загружено из файлов, либо создан программным путем) для указания geometry с новым [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) класса.

<a name="SpriteKit-Enhancements" />

## <a name="spritekit-enhancements"></a>Усовершенствования SpriteKit

Платформа SpriteKit в tvOS 10 были внесены следующие улучшения:

 - Tilemaps теперь поддерживают square, Шестигранная и изометрической плитки фигур для двухмерных, 2,5 D и прокрутка стороне игры, используя `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` и `SKTileSet` классы.
 - Используйте новый `SKWarpGeometry` класса к растягиванию или искажать [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) или [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) подготовки к просмотру. Новый [SKAction](https://developer.apple.com/reference/spritekit/skaction) класс может использоваться для переходов между warp эффекты анимации.
 - Пользовательские построители текстуры можно предоставить атрибуты (`SKAttribute`), можно настроить по отдельности каждым узлом, который использует шейдера, указав значение атрибута (`SKAttributeValue`).
 - [SKView](https://developer.apple.com/reference/spritekit/skview) класс предоставляет несколько новых способов позволяют точно контролировать время и способ визуализации сцены.

<a name="UIKit-Enhancements" />

## <a name="uikit-enhancements"></a>Усовершенствования UIKit

На платформе UIKit в tvOS 10 были внесены следующие улучшения:

 - Фокус API был улучшен для поддержки фокуса элемента не view в дополнение к `UIViews`. Элементы, которые поддерживают фокус _необходимо_ реализовать `IUIFocusItem` интерфейс.
 - Новый `UIGraphicsRender` класс предоставляет метод объектно ориентированного создания растровые изображения или PDF-файлы из UIKit отрисовки или Core Graphics и заменяет нерекомендуемый `UIGraphicsBeginImageContext` метод.
 - `UIUserInterfaceStyle` Был добавлен класс, чтобы определить, какие темы пользовательского интерфейса (Темная или Светлая) в данный момент активна.
 - Добавлена поддержка нового, полностью интерактивные, объектно ориентированного прерываемые анимации и Ван связываться с помощью жестов. Pleas см. в разделе Apple [по протоколу UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [ссылки на класс UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [по протоколу UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Ссылки на класс UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) и [ссылки на класс UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) Дополнительные сведения.
 - Новый `UIPreviewInteraction` и `UIPreviewInteractionDelegate` разрешить приложению приложения предоставляют пользовательский интерфейс для операций считывания и pop.
 - Новый `UIAccessibilityCustomRotor` класс позволяет приложению предоставлять пользовательские, зависящего от контекста функциональные возможности для вспомогательных технологий, таких как Voice Over.
 - Используйте `UIAccessibilityIsAssistiveTouchRunning` и `UIAccessibilityAssistiveTouchStatusDidChangeNotification` символы, чтобы определить, включена ли AssistiveTouch.
 - Используйте `UIAccessibilityHearingDevicePairedEar` и `UIAccessibilityHearingDevicePairedEarDidChangeNotification` символы, чтобы получить состояние какой-либо пару вспомогательные средства MFi вашим отзывам.
 - Новый [UIPasteboard](https://developer.apple.com/reference/uikit/uipasteboard) API предоставляет новые возможности (например, время существования ограничения) и автоматически объявляют совместимых типов содержимого для распространенных типов классов.
 - Для поддержки динамического типа в метки, текстовые поля и текстовые поля используйте новый `PreferredFontForTextStyle` метод `UIFont` класса.
 - Чтобы решить, если элемент необходимо обновить его шрифта при устройства `UIContentSizeCategory` изменения, используйте `AdjustsFontForContentSizeCategory` свойство `UIContentSizeCategoryAdjusting` делегировать.
 - Приложение теперь может управлять внешний вид значка для панели элементов вкладки, например цвет текста и фона.
 - Обновление экрана в теперь поддерживается во всех представления и представления подклассы (например, `UICollectionView`).
 - `OpenURL` Метод `UIApplication` класса вызывается асинхронно теперь поддерживает обработчик завершения, который вызывается после завершения открытия.
 - Инициировать CloudKit общий доступ и изменение его свойств, с помощью нового `UICloudSharingController` и `UICloudSharingControllerDelegate` классы.
 - Воспользоваться преимуществами предварительно выбранных ячеек прокрутки совершенствовать `UICollectionViews` с новым `UICollectionViewDataSourcePrefetching` делегировать.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Новые возможности в tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
