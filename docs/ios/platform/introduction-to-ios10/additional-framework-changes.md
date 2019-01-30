---
title: Изменения платформы дополнительных iOS 10
description: В этом документе описываются незначительные изменения и усовершенствования, внесенные в имеющиеся платформы в iOS 10 и обсуждаются способы сделать использование этих обновлений в Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 0E2217F1-FC96-4D0A-ABAB-D40AD8F96502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/29/2017
ms.openlocfilehash: a7b029aad69e65192d48d969dba2b9bb9a0d7a50
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233904"
---
# <a name="additional-ios-10-frameworks-changes"></a>Изменения платформы дополнительных iOS 10

_В этой статье рассматриваются дополнительные, незначительные изменения и улучшения для существующих платформ для iOS 10._

## <a name="av-foundation-framework-additions"></a>Дополнения Framework AV Foundation

Платформа AVFoundation включает следующие улучшения:

- В iOS 10, разработчик больше не нужно реализовывать различные [AVPlayerItem](https://developer.xamarin.com/api/type/AVFoundation.AVPlayerItem/) поведения на основе типа содержимого. Просто задайте `Rate` свойство и AVFoundation определяют при достаточно содержимое доступно для воспроизведения без замедляется работа.
- Новый [AVCapturePhotoOutput](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureFileOutput/) заменяет нерекомендуемый класс `AVCaptureStillImageOutput` класса и предоставляет единый метод для обработки всех фотографий рабочих процессов, предоставляя расширенного управления и наблюдение за процессом отслеживания и Поддержка новых функций, таких как фотографии Live и НЕОБРАБОТАННЫЕ записи формат.
- Новый `AVPlayerLooper` класс упрощает цикл данной части носителей во время воспроизведения.
- `AVAssetDownloadURLSession` Класс позволяет получить загрузку и более поздних версий воспроизведение FairPlay зашифрованы потоков HLS.
- По умолчанию [AVCaptureSession](https://developer.xamarin.com/api/type/AVFoundation.AVCaptureSession/) класс автоматически поддерживает захват wide-color, широким, когда его поддерживает устройства. См. в разделе Apple [Справочник по совместимости устройства iOS](https://developer.apple.com/library/prerelease/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013599) для получения дополнительных сведений.

## <a name="avkit-additions"></a>Дополнения AVKit

Платформа AVKit теперь включает новый `UpdatesNowPlayingInfoCenter` свойство, указывающее, когда следует обновить информационный центр игральную теперь.

## <a name="core-data-enhancements"></a>Усовершенствования основных данных

iOS 10, включает следующие улучшения в Framework Core Data:

- [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) объектов с хранилищами данных SQLite в режиме журнал WAL поддержки нового поколения запроса компонентов, где управляемый объект контекстов (MOC) можно прикрепить к версии конкретной базы данных для будущих выборки и Ошибка транзакции.
- Корневой [NSManagedObjectContext](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectContext/) объектов поддерживает параллельные завершение со сбоем и выборка без сериализации.
- [NSPersistentStoreCoordinator](https://developer.xamarin.com/api/type/CoreData.NSPersistentStoreCoordinator/) класс поддерживается пул хранилищ данных SQLite.
- Были добавлены несколько новых удобных методов `NSManagedObject` облегчая процесс для выполнения операций выборки и создание подклассов.
- С помощью высокоуровневым `NSPersistenceContainer` ссылка `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.xamarin.com/api/type/CoreData.NSManagedObjectModel/) и другим ресурсам конфигурации Core Data.

Дополнительные сведения см. в разделе Apple [справочные материалы по основной данных Framework](https://developer.apple.com/reference/coredata).

## <a name="core-image-enhancements"></a>Главные усовершенствования образа

iOS 10 вносит следующие усовершенствования framework образ основных компонентов:

- Разработчику возможность обрабатывать изображения в цветовом пространстве вне контекста образ основных компонентов рабочее цветовое пространство путем преобразования в и из него цветовое пространство до и после обработки.
- Для устройств iOS, использующие A8 или A9 ЦП теперь поддерживается формат изображений RAW-ФОРМАТА. Основной образ теперь обеспечивает поддержку для декодирования НЕОБРАБОТАННЫЕ образы либо из встроенных iSight камеры или с сторонних производителей камеры. Используйте `FilterWithImageData` или `FilterWithImageURL` методы [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) класс для обработки НЕОБРАБОТАННЫХ изображений.
- Были внесены некоторые улучшения производительности отрисовки `UIImage` визуализации (когда хранилищ образа образ основных компонентов) в `UIImageView` объектов. 
- `UIImage` объекты с тегами wide-цветовой гаммы визуализирует цвета в цветовой гаммы wide в `UIImageView` объекты на устройствах iOS, поддерживающих широкая цветовая палитра.
- Основной образ ядра код теперь можете запросить определенный пиксель выходных форматов.
- `ImageWithExtent` Метод [CIFilter](https://developer.xamarin.com/api/type/CoreImage.CIFilter/) класс может использоваться для вставки пользовательской обработки в операцию фильтрации. Основной образ будет вызывать данным обратным вызовом от фильтров при обработке изображения для выходных данных или отображения.

Кроме того были добавлены следующие новые фильтры образ основных компонентов:

- `CINinePartTiled`
- `CINinePartStretched`
- `CIHueSaturationValueGradient`
- `CIEdgePreserveUpsampleFilter`
- `CIClamp`

## <a name="core-motion-additions"></a>Дополнения движения Core

Новое в iOS 10, платформа движения Core включает pedometer события, которые позволяют приложению быстрых и получать уведомления в реальном времени пользователя, приостановки и возобновления отслеживания во время выполнения. Используйте [CMPedometer](https://developer.xamarin.com/api/type/CoreMotion.CMPedometer/) для регистрации событий pedometer обычном или фоновом режиме.

## <a name="foundation-enhancements"></a>Усовершенствования Foundation

В среде Foundation для iOS 10 были внесены следующие улучшения:

- Используйте новый [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) класс для форматирования локализованного измерения для отображения для конечного пользователя.
- Используйте новый [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) класса, чтобы дата и время вычисления интервала, например длительности для сравнения интервалы и тестирование для пересечения интервал.
- Используйте новый [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) класс для преобразования между разные единицы из мер (единица Измерения) или выполняют вычисления над значениями в разных UOMs.

- Используйте новый [NSUnit](https://developer.apple.com/reference/foundation/nsunit) и [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) классы для представления определенных UOMs.
- Были добавлены несколько новых свойств [NSLocal](https://developer.apple.com/reference/foundation/nslocale) класс получения местной информации и отображение доступных форматов.

## <a name="gamekit-enhancements"></a>Усовершенствования GameKit

Платформа "GameKit" в iOS 10 были внесены следующие улучшения:

- **Приложения Game Center** устаревшими и удалены из iOS. Если приложение использует GameKit, его _необходимо_ предоставить свой собственный интерфейс для отображения GameKit функции, такие как списки лидеров и т. д. 
- Реализован новый тип учетную запись iCloud [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) класса.
- Новый [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) класс предоставляет универсального решения для управления хранилищем постоянных данных в Game Center. `GKGameSession` хранит список игроков и приложение отвечает за реализацию как и когда участника дата хранится, извлечь или обмениваются игроков. Во многих случаях игр сеансов можно заменить существующих Поочередный совпадений, совпадений в режиме реального времени или постоянных игры сохранить методы.

## <a name="gameplaykit-enhancements"></a>Усовершенствования GameplayKit

Платформа GameplayKit в iOS 10 были внесены следующие улучшения:

- Используйте новый [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) класса для обеспечения высокой производительности — это Естественный пути.
- Поколение процедурного пропускаемые была добавлена и может использоваться для повышения реалистичности в выглядящие естественно текстуры, добавить реализм перемещение камеры и помогают создавать полнофункциональной игровой среды.
- Используйте Пространственные секционирования для секционирования данных игровой для эффективного поиска.
- Специалист по стратегиям новый методом Монте-Карло ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) был добавлен для вычислений исчерпывающим возможных перемещения.
- Поддержка трехмерного представления был добавлен существующий агент и поведения путь поиска, с помощью нового [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) и [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) классы.
- Новый [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) и [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) классы марки, объединение GameplayKit и проще, чем когда-либо SpriteKit.
- Добавлен новый интерфейс API дерева принятия решений ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) и [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) для улучшения AI игры дом.

## <a name="healthkit-enhancements"></a>Усовершенствования HealthKit

Платформа HealthKit в iOS 10 были внесены следующие улучшения:

- Были добавлены новые ключи метаданных для типов погоды (такие как `HKWeatherConditionClear` и `HKWeatherConditionCloudy`) и для организации тренировок типов (таких как `HKWorkoutActivityTypeFlexibility` и `HKWorkoutActivityTypeWheelchairRunPace`) были добавлены.
- Новый `HKCDADocument` был добавлен класс для представления архитектуры клинических документов (CDA) в формате документа.
- Используйте новый [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) класс для указания `ActivityType` и `LocationType` для тренировок.
- Новый [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) и `WheelchairUse` метод [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) класса были добавлены для работы с колясок связанные данные о работоспособности.

## <a name="homekit-enhancements"></a>Усовершенствования HomeKit

Фреймворк HomeKit в iOS 10 были внесены следующие улучшения:

- Были добавлены новые службы и характеристики.
- IPad можно настроить для использования в качестве концентратора для предоставления удаленного доступа периферийных устройств, запустить триггеры автоматизации и включить HomeKit общих разрешений пользователя.
- Добавлена поддержка для камеры и дверного звонка стандартные.
- Для "Стандартные" указаны дополнительные контекста и конфигурации.

См. в нашем [введение в HomeKit](~/ios/platform/homekit.md) Дополнительные сведения см.

## <a name="metal-enhancements"></a>Усовершенствования исходного состояния системы

К платформе без операционной системы в iOS 10 были внесены следующие улучшения:

- Теперь можно использовать трехмерных приложениях и играх _тесселяции_ для эффективного отображения сложных сцен и geometry с помощью графического Процессора.
- Предоставлять детальный контроль выделения ресурсов для оптимизации работы операционной системы на основе приложений с помощью ресурсов кучи и Memoryless целевых буферов визуализации.
- Используйте специализацию функции для создания коллекции оптимизирован материалов и света сочетания функций для сцены.

Дополнительные сведения см. в разделе Apple [руководство по программированию без операционной системы](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

## <a name="modelio-enhancements"></a>Усовершенствования ModelIO

Платформа ModelIO в iOS 10 были внесены следующие улучшения:

 - Теперь поддерживается формат файла долл. США.
 - Подпись поля расстояние, добавлена поддержка [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) класса.
 - Используйте новый `MDLLightProbeIrradianceDataSource` класс, помогающий в свет выборки данных размещения.
 - Используйте новый `MDLMaterialPropertyGraph` класса для упрощения поддержки среды выполнения изменения в модели.

## <a name="photos-enhancements"></a>Усовершенствования фотографий

Платформа фотографий в iOS 10 были внесены следующие улучшения:

- Используйте [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) и [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) классы, чтобы воспользоваться преимуществами нового образа основных компонентов процессора для внесения изменений.
- Интерактивное редактирование фотографий теперь доступна для приложений, поддерживающие платформу фотографии и расширения редактирования фотографий (для использования внутри фотографии и камеры приложений).
- Используйте новый [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) класс для применения изменений к видео и по-прежнему содержимое Live фотографий.

## <a name="replaykit-enhancements"></a>Усовершенствования ReplayKit

Платформа ReplayKit в iOS 10 были внесены следующие улучшения:

- Используйте [RPScreenRecorder](https://developer.apple.com/reference/replaykit/rpscreenrecorder), [RPBroadcastActivityViewController](https://developer.apple.com/reference/replaykit/rpbroadcastactivityviewcontroller) и [RPBroadcastController](https://developer.apple.com/reference/replaykit/rpbroadcastcontroller) классы для поддержки передачи широковещательных сообщений из записанных содержимое с помощью третьей стороной сайты.
- Расширения пользовательского интерфейса вещания и отправьте вещания требуются для поддержки широковещательных служб сторонних производителей ReplayKit 3-й день в приложении.

## <a name="scenekit-enhancements"></a>Усовершенствования SceneKit

Платформа SceneKit IOS 10 были внесены следующие улучшения:

- [SCNCamera](xref:SceneKit.SCNCamera) класс может предоставить больше реализм с помощью функций HDR и эффекты. Используйте адаптивной раскрытия для создания автоматического эффекты или виньетирование использования, каймы цвет и цвет градации Добавление fillmatic эффекты для игры.
- SceneKit теперь включает в себя новую систему физически основе визуализации (PBR) для более реалистичного результатов разработки, более простой ресурс.
- Используйте новый [SCNLightingModelPhysicallyBased](https://developer.apple.com/reference/scenekit/scnlightingmodelphysicallybased) заливки модель, в которой продукта широкий спектр реалистичную заливку эффекты при этом не только для трех основных свойств (`Diffuse`, `Metalness` и `Roughness`).
- С момента PBR заливки работает лучше всего с освещением, основанная на среде, используйте `LightingEnvironment` свойств образа освещения сцены целиком.
- Используйте `IESProfileURL` значение для импорта приборами реального мира, которые определяют освещения в зависимости от реальных значений, таких как интенсивность (в люмен) и цветовой температуры (в градусах Кельвина).
- PBR и HDR камеры функции обеспечивают лучшие результаты, чем методы традиционных отрисовки и, таким образом, SceneKit теперь выполняет все вычисления цвета в линейном цветовом пространстве (с использованием цветового P3 дисплеям устройств wide цвет).
- SceneKit теперь цвет соответствует все цвета, считывая данные профиля цвета.
- SceneKit интерпретирует значений составляющих цвета в линейной цветовое пространство RGB для всех типов шейдера.
- Линейные цветовые пространства отрисовки и расширенных цвет можно отключить, задав `SCNDisableLinearSpaceRendering` и `SCNDisableWideGamut` ключей в приложении `Info.plist`.
- Создавать произвольные многоугольника приматов (либо загружено из файлов, либо создан программным путем) для указания geometry с новым [SCNGeometryPrimitiveTypePolygon](https://developer.apple.com/reference/scenekit/1772322-scenekit_enumerations/scngeometryprimitivetype/scngeometryprimitivetypepolygon) класса.
- Поскольку SceneKit считывает и настроить сведения о профиле цвет в изображения текстур, используйте каталоги активов для всех образов, чтобы убедиться, что эта информация.

## <a name="spritekit-enhancements"></a>Усовершенствования SpriteKit

Платформа SpriteKit IOS 10 были внесены следующие улучшения:

- Пользовательские построители текстуры можно предоставить атрибуты (`SKAttribute`), можно настроить по отдельности каждым узлом, который использует шейдера, указав значение атрибута (`SKAttributeValue`).
- Tilemaps теперь поддерживают square, Шестигранная и изометрической плитки фигур для двухмерных, 2,5 D и прокрутка стороне игры, используя `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` и `SKTileSet` классы.
- Используйте новый `SKWarpGeometry` класса к растягиванию или искажать [SKSpriteNode](https://developer.xamarin.com/api/type/SpriteKit.SKSpriteNode/) или [SKEffectNode](https://developer.xamarin.com/api/type/SpriteKit.SKEffectNode/) подготовки к просмотру. Новый [SKAction](https://developer.xamarin.com/api/type/SpriteKit.SKAction/) класс может использоваться для переходов между warp эффекты анимации.
- [SKView](https://developer.xamarin.com/api/type/SpriteKit.SKView/) класс предоставляет несколько новых способов позволяют точно контролировать время и способ визуализации сцены.

## <a name="scrollview-enhancements"></a>Усовершенствования ScrollView

Элемент управления ScrollView в iOS 10.3 были внесены следующие улучшения:

- `UIScrollView` Теперь включают `IndexDisplayMode` свойства для управления отображением индекс при прокрутке пользователем как `UIScrollViewIndexDisplayMode` из:
    - `Automatic` — Отображение контролируется Операционной системой.
    - `AlwaysHidden` — Отображение всегда является скрытым.

См. в разделе [iOSTenThree пример](https://developer.xamarin.com/samples/monotouch/iOS10/iOSTenThree) для использования.

## <a name="uikit-enhancements"></a>Усовершенствования UIKit

На платформе UIKit в iOS 10 были внесены следующие улучшения:

- Новый [UIPasteboard](xref:UIKit.UIPasteboard) API предоставляет новые возможности (например, время существования ограничения) и автоматически объявляют совместимых типов содержимого для распространенных типов классов.
- Поддержка новых анимации, полностью интерактивные, объектно ориентированного прерываемой был добавлен и могут быть связаны с помощью жестов. См. в разделе Apple [по протоколу UIViewAnimating](https://developer.apple.com/reference/uikit/uiviewanimating), [ссылки на класс UIViewPropertyAnimator](https://developer.apple.com/reference/uikit/uiviewpropertyanimator), [по протоколу UITimingCurveProvider](https://developer.apple.com/reference/uikit/uitimingcurveprovider), [Ссылки на класс UICubicTimingParameters](https://developer.apple.com/reference/uikit/uicubictimingparameters) и [ссылки на класс UISpringTimingParameter](https://developer.apple.com/reference/uikit/uispringtimingparameters) Дополнительные сведения.
- Новый `UIPreviewInteraction` и `UIPreviewInteractionDelegate` разрешить приложению разработчика для обеспечения пользовательского интерфейса для операций считывания и pop.
- Новый `UIAccessibilityCustomRotor` класс позволяет приложению предоставлять пользовательские, зависящего от контекста функциональные возможности для вспомогательных технологий, таких как Voice Over.
- Используйте `UIAccessibilityIsAssistiveTouchRunning` и `UIAccessibilityAssistiveTouchStatusDidChangeNotification` символы, чтобы определить, включена ли AssistiveTouch.
- Используйте `UIAccessibilityHearingDevicePairedEar` и `UIAccessibilityHearingDevicePairedEarDidChangeNotification` символы, чтобы получить состояние какой-либо пару вспомогательные средства MFi вашим отзывам.
- Для поддержки динамического типа в метки, текстовые поля и текстовые поля используйте новый `PreferredFontForTextStyle` метод `UIFont` класса.
- Чтобы решить, если элемент необходимо обновить его шрифта при устройства `UIContentSizeCategory` изменения, используйте `AdjustsFontForContentSizeCategory` свойство `UIContentSizeCategoryAdjusting` делегировать.
- `OpenURL` Метод `UIApplication` класса вызывается асинхронно и теперь поддерживает обработчик завершения, который вызывается после завершения откройте действие.
- Инициировать CloudKit общий доступ и изменение его свойств, с помощью нового `UICloudSharingController` и `UICloudSharingControllerDelegate` классы.
- Воспользоваться преимуществами предварительно выбранных ячеек прокрутки совершенствовать `UICollectionViews` с новым `UICollectionViewDataSourcePrefetching` делегировать.
- Разработчик теперь можно управлять внешним видом эмблемы для элементов этой панели (например, текст и цвет фона).
- Обновление экрана теперь поддерживается во всех представления и представления подклассы прокрутки (таких как `UICollectionView`).

## <a name="webkit-enhancements"></a>Усовершенствования WebKit

Платформа WebKit в iOS 10 были внесены следующие улучшения:

- Peek и pop поддержки был добавлен `WKWebView` класса. Используйте `ShouldPreviewElement` метод, чтобы определить, если данной веб-представление следует отобразить предварительный просмотр.


## <a name="related-links"></a>Связанные ссылки

- [Примеры iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Новые возможности в iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewIniOS/Articles/iOS10.html#//apple_ref/doc/uid/TP40017084-SW1)
