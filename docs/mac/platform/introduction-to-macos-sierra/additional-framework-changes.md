---
title: Дополнительные изменения платформы macOS Sierra
description: В этом документе описываются незначительные изменения и усовершенствования существующих платформ, представленные в macOS Sierra. Он проверяет изменения framework ускорение, AppKit, AVFoundation, Core Data, образ основных компонентов, Foundation и многое другое.
ms.prod: xamarin
ms.assetid: CA701269-D11E-4DE3-89C1-58EF8993A482
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 6ed827c018931e5b79887dc355f136e2a84623d6
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50111055"
---
# <a name="additional-macos-sierra-framework-changes"></a>Дополнительные изменения платформы macOS Sierra

<a name="Accelerate-Framework-Enhancements" />

## <a name="accelerate-framework-enhancements"></a>Ускорьте усовершенствования Framework

Ускорьте платформе для macOS Sierra были внесены следующие усовершенствования:

- Добавлена Quadrature (целочисленный тип математического анализа).
- Добавлена базовых функций для построения нейронных сетей.
- Добавлена геометрические функциях предиката, чтобы проверить такие пересечения двух геометрических объектов.

<a name="AppKit-Framework-Enhancements" />

## <a name="appkit-framework-enhancements"></a>Усовершенствования AppKit Framework

Платформа AppKit для macOS Sierra были внесены следующие усовершенствования:

- Несколько усовершенствований `NSCollectionView` такие как:
    - **Свертываемых** -позволяет пользователю свернуть раздел представление коллекции в одну строку по горизонтали.
    - **С плавающей запятой заголовки** -верхние и нижние колонтитулы, теперь могут быть перемещенное (в виде сплошного потока) используя один API как [UICollectionView](https://developer.apple.com/reference/uikit/uicollectionview) в iOS.
    - **Прокручиваемые фона представления** -фона представления коллекции теперь можно указать значение для прокрутки вместе с содержимым.
- Проход разметки отложенного представления оптимизирован и расширенный.
- API-Интерфейс и перетащите теперь включает новый `NSFilePromiseProvider` и `NSFilePromiseReceiver` перетащите классы для поддержки или косяк.
- Несколько конструкторов удобства добавлены к существующим элементам управления.
    -  `NSButton` включает в себя новые конструкторы для создания кнопки, выбора флажков и переключателей.
    -  `NSTextField` включает в себя новые конструкторы для создания меток не переноса и режим переноса строк, с атрибутами метки и изменяемые текстовые поля.
    -  `NSSegmentedControl` включает в себя новые конструкторы для создания Сегментированные элементы управления из группы меток и изображений.
    -  `NSSlider` включает в себя новые конструкторы для создания горизонтального линейного ползунки.
    -  `NSImageView` включает в себя новые конструкторы для создания представления неизменяемые изображения из заданного `NSImage`.
-  Новый `NSGridView` был добавлен в коллекцию представлений sub в сетку с переменной размера строки и столбцы, которые могут быть динамически скрыто или показано автоматический макет.

<a name="AVFoundation-Framework-Enhancements" />

## <a name="avfoundation-framework-enhancements"></a>Усовершенствования AVFoundation Framework

Платформа AVFoundation для macOS Sierra были внесены следующие усовершенствования:

- В macOS, приложение больше не нужно реализовывать различные [AVPlayerItem](https://developer.apple.com/reference/avfoundation/avplayeritem) поведения на основе типа содержимого. Просто задайте `Rate` свойство и AVFoundation определяют при достаточно содержимое доступно для воспроизведения без замедляется работа.
- Новый `AVPlayerLooper` класс упрощает цикл данной части носителей во время воспроизведения.
- `AVAssetDownloadURLSession` Класс позволяет получить загрузку и более поздних версий воспроизведение FairPlay зашифрованы потоков HLS.

<a name="Core-Data-Framework-Enhancements" />

## <a name="core-data-framework-enhancements"></a>Усовершенствования Framework Core данных

Основная платформа данных для macOS Sierra были внесены следующие усовершенствования:

- Корневой [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) объектов поддерживает параллельные завершение со сбоем и выборка без сериализации.
- [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) класс поддерживается пул хранилищ данных SQLite.
- [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) объектов с хранилищами данных SQLite в режиме журнал WAL поддержки нового поколения запроса компонентов, где управляемый объект контекстов (MOC) можно прикрепить к версии конкретной базы данных для будущих выборки и Ошибка транзакции.
- С помощью высокоуровневым `NSPersistenceContainer` ссылка `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) и другим ресурсам конфигурации Core Data.
- Были добавлены несколько новых удобных методов `NSManagedObject` облегчая процесс для выполнения операций выборки и создание подклассов.

Дополнительные сведения см. в разделе Apple [справочные материалы по основной данных Framework](https://developer.apple.com/reference/coredata).

<a name="Core-Image-Framework-Enhancements" />

## <a name="core-image-framework-enhancements"></a>Усовершенствования Framework Core образа

Основная платформа образа для macOS Sierra были внесены следующие усовершенствования:

- `ImageWithExtent` Метод [CIFilter](https://developer.apple.com/reference/coreimage/cifilter) класс может использоваться для вставки пользовательской обработки в операцию фильтрации. Основной образ будет вызывать данным обратным вызовом от фильтров при обработке изображения для выходных данных или отображения.
- Приложение теперь может обрабатывать образы в цветовом пространстве вне контекста образ основных компонентов рабочее цветовое пространство путем преобразования в и из него цветовое пространство до и после обработки.
- Образ основных компонентов ядра теперь можете запросить определенный пиксель формат вывода.
- Были добавлены следующие новые фильтры образа: `CINinePartTitled`, `CINinePartStretched`, `CIHueSaturationValueGradient`, `CIEdgePreserveUpsampleFilter` и `CIClamp`.

<a name="Foundation-Framework-Enhancements" />

## <a name="foundation-framework-enhancements"></a>Усовершенствования Framework Foundation

В среде Foundation для macOS Sierra были внесены следующие усовершенствования:

- Используйте [NSDimentions](https://developer.apple.com/reference/foundation/nsdimension) масс API для представления, преобразование и отображение многие из наиболее распространенных физические единицы, такие как длина, скорости, длительность и температуры.
- Используйте [NSISO8601DateFormatter](https://developer.apple.com/reference/foundation/nsiso8601dateformatter) класс для синтаксического анализа и создания ISO 8601 отформатированные даты.
- Используйте новый [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) класса, чтобы дата и время вычисления интервала, например длительности для сравнения интервалы и тестирование для пересечения интервал.
- Используйте [NSPersonNameComponentsFormatter](https://developer.apple.com/reference/foundation/nspersonnamecomponentsformatter) класс для синтаксического анализа элементов имени человека из строки.
- Используйте новый [NSURLSessionTaskMetrics](https://developer.apple.com/reference/foundation/nsurlsessiontaskmetrics) для получения метрик для URL-адреса, сетевые подключения сеанса.

Дополнительные сведения см. в разделе Apple [Foundation заметки о выпуске v10.12 OS X и iOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/RN-Foundation-OSX10.12/index.html).

<a name="GameKit-Framework-Enhancements" />

## <a name="gamekit-framework-enhancements"></a>Усовершенствования GameKit Framework

Платформа "GameKit" для macOS Sierra были внесены следующие усовершенствования:

- **Приложения Game Center** устаревшими и удалены из macOS. Если приложение использует GameKit, его _необходимо_ предоставить свой собственный интерфейс для отображения GameKit функции, такие как списки лидеров и т. д. 
- Реализован новый тип учетную запись iCloud [GKCloudPlayer](https://developer.apple.com/reference/gamekit/gkcloudplayer) класса.
- Новый [GKGameSession](https://developer.apple.com/reference/gamekit/gkgamesession) класс предоставляет универсального решения для управления хранилищем постоянных данных в Game Center. `GKGameSession` хранит список игроков и приложение является ответственным формы реализации, как и когда участника дата хранится, извлечь или обмениваются игроков. Во многих случаях игр сеансов можно заменить существующих Поочередный совпадений, совпадений в режиме реального времени или постоянных игры сохранить методы.

<a name="GamePlayKit-Framework-Enhancements" />

## <a name="gameplaykit-framework-enhancements"></a>Усовершенствования GamePlayKit Framework

Платформа GamePlayKit для macOS Sierra были внесены следующие усовершенствования:

- Поколение процедурного пропускаемые была добавлена и может использоваться для повышения реалистичности в выглядящие естественно текстуры, добавить реализм перемещение камеры и помогают создавать полнофункциональной игровой среды.
- Используйте Пространственные секционирования для секционирования данных игровой для эффективного поиска.
- Специалист по стратегиям новый методом Монте-Карло ([GKMonteCarloStrategist](https://developer.apple.com/reference/gameplaykit/gkmontecarlostrategist)) был добавлен для вычислений исчерпывающим возможных перемещения.
- Добавлен новый интерфейс API дерева принятия решений ([GKDecisionTree](https://developer.apple.com/reference/gameplaykit/gkdecisiontree) и [GKDecisionNode](https://developer.apple.com/reference/gameplaykit/gkdecisionnode)) для улучшения AI игры дом.
- Поддержка трехмерного представления был добавлен существующий агент и поведения путь поиска, с помощью нового [GKAgent3D](https://developer.apple.com/reference/gameplaykit/gkagent3d) и [GKGraphNode3D](https://developer.apple.com/reference/gameplaykit/gkgraphnode3d) классы.
- Используйте новый [GKMeshGraph](https://developer.apple.com/reference/gameplaykit/gkmeshgraph) класса для обеспечения высокой производительности — это Естественный пути.
- Новый [GKScene](https://developer.apple.com/reference/gameplaykit/gkscene) и [GKSKNodeComponent](https://developer.apple.com/reference/gameplaykit/gksknodecomponent) классы марки, объединение GameplayKit и проще, чем когда-либо SpriteKit.

<a name="Metal-Framework-Enhancements" />

## <a name="metal-framework-enhancements"></a>Усовершенствования системы Framework

Платформе без операционной системы для macOS Sierra были внесены следующие усовершенствования:

- Теперь можно использовать трехмерных приложениях и играх _тесселяции_ для эффективного отображения сложных сцен и geometry с помощью графического Процессора.
- Используйте специализацию функции для создания коллекции оптимизирован материалов и света сочетания функций для сцены.
- Предоставлять детальный контроль выделения ресурсов для оптимизации работы операционной системы на основе приложений с помощью ресурсов кучи и Memoryless целевых буферов визуализации.

Дополнительные сведения см. в разделе Apple [руководство по программированию без операционной системы](https://developer.apple.com/library/prerelease/content/documentation/Miscellaneous/Conceptual/MetalProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014221).

<a name="ModelIO-Framework-Enhancements" />

## <a name="model-io-framework-enhancements"></a>Улучшения модели Framework ввода-вывода

Платформа операций ввода-вывода модели для macOS Sierra были внесены следующие усовершенствования:

- Теперь поддерживается формат файла долл. США.
- Используйте новый `MDLMaterialPropertyGraph` класса для упрощения поддержки среды выполнения изменения в модели.
- Подпись поля расстояние, добавлена поддержка [MDLVoxelArray](https://developer.apple.com/reference/modelio/mdlvoxelarray) класса.
- Используйте новый `MDLLightProbeIrradianceDataSource` класс, помогающий в свет выборки данных размещения.

<a name="Photos-Framework-Enhancements" />

## <a name="photos-framework-enhancements"></a>Усовершенствования Framework фотографий

Платформа фотографии для macOS Sierra были внесены следующие усовершенствования:

- Интерактивное редактирование фотографий теперь доступна для приложений, поддерживающие платформу фотографии и расширения редактирования фотографий (для использования внутри фотографии и камеры приложений).
- Используйте новый [PHLivePhotoEditingContext](https://developer.apple.com/reference/photos/phlivephotoeditingcontext) класс для применения изменений к видео и по-прежнему содержимое Live фотографий.
- Используйте [CIImageProcessorInput](https://developer.apple.com/reference/coreimage/ciimageprocessorinput) и [CIImageProcessorOutput](https://developer.apple.com/reference/coreimage/ciimageprocessoroutput) классы, чтобы воспользоваться преимуществами нового образа основных компонентов процессора для внесения изменений.
- Для поддержки Live фотографий, [PHLivePhoto](https://developer.apple.com/reference/photos/phlivephoto) и [PHLivePhotoView](https://developer.apple.com/reference/photosui/phlivephotoview) классы были перенесены из iOS macOS.

<a name="SceneKit-Framework-Enhancements" />

## <a name="scenekit-framework-enhancements"></a>Усовершенствования SceneKit Framework

Платформа SceneKit для macOS Sierra были внесены следующие усовершенствования:

- Теперь включает в себя новую систему физически основе визуализации (PBR) для более реалистичного результатов разработки, более простой ресурс.
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

<a name="Security-Framework-Enhancements" />

## <a name="security-framework-enhancements"></a>Усовершенствования безопасности Framework

Платформа безопасности для macOS Sierra были внесены следующие усовершенствования:

- `SecKey` Интерфейс модернизировать и единой для всех платформ (iOS, tvOS, watchOS и macOS).

<a name="SpriteKit-Framework-Enhancements" />

## <a name="spritekit-framework-enhancements"></a>Усовершенствования SpriteKit Framework

Платформа SpriteKit для macOS Sierra были внесены следующие усовершенствования:

- Tilemaps теперь поддерживают square, Шестигранная и изометрической плитки фигур для двухмерных, 2,5 D и прокрутка стороне игры, используя `SKTileMapMode`, `SKTileGroup`, `SKTileGroupRule` и `SKTileSet` классы.
- Используйте новый `SKWarpGeometry` класса к растягиванию или искажать [SKSpriteNode](https://developer.apple.com/reference/spritekit/skspritenode) или [SKEffectNode](https://developer.apple.com/reference/spritekit/skeffectnode) подготовки к просмотру. Новый [SKAction](https://developer.apple.com/reference/spritekit/skaction) класс может использоваться для переходов между warp эффекты анимации.
- Пользовательские построители текстуры можно предоставить атрибуты (`SKAttribute`), можно настроить по отдельности каждым узлом, который использует шейдера, указав значение атрибута (`SKAttributeValue`).
- [SKView](https://developer.apple.com/reference/spritekit/skview) класс предоставляет несколько новых способов позволяют точно контролировать время и способ визуализации сцены.

<a name="New-Frameworks" />

## <a name="new-frameworks"></a>Новых платформ

MacOS Sierra были добавлены следующие платформы:

- **Объекты Intent Framework** -эта платформа позволяет приложению для проверки взаимодействия (например, расположение или пользователь действия) и предпринять действия на основе этой информации.
- **SafariServices Framework** -эта платформа позволяет приложению для разработки приложения расширений для обозревателя Safari (такие как препятствия для содержимого) для устройств iOS и macOS.

## <a name="related-links"></a>Связанные ссылки

- [Примеры Mac](https://developer.xamarin.com/samples/mac/)
- [Новые возможности в OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
