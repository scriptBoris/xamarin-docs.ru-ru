---
title: Общие сведения о ARKit в Xamarin.iOS
description: В этом документе описывается дополненная реальность в iOS 11 с ARKit. Он описывает добавление трехмерной модели в приложение, настроить представление, реализации делегата сеанса, поместите трехмерной модели в мире и приостановить сеанс режиме дополненной реальности.
ms.prod: xamarin
ms.assetid: 70291430-BCC1-445F-9D41-6FBABE87078E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 08/30/2017
ms.openlocfilehash: 14bbb35477c098738e9cd7e2cb92154422d394ee
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350619"
---
# <a name="introduction-to-arkit-in-xamarinios"></a>Общие сведения о ARKit в Xamarin.iOS

_Дополненная реальность для iOS 11_

ARKit включает разнообразные дополненная реальность приложений и игр. Данный раздел охватывает следующее:

- [Приступая к работе с ARKit](#gettingstarted)
- [С помощью ARKit с UrhoSharp](urhosharp.md)

<a name="gettingstarted" />

## <a name="getting-started-with-arkit"></a>Приступая к работе с ARKit

Чтобы начать работу с дополненная реальность, приведенные ниже инструкции рассмотрим простое приложение: размещение трехмерной модели и позволить ARKit Сохраните модель в месте с функциональными возможностями отслеживания.

![Jet трехмерной модели с плавающей запятой в camera образа](images/jet-sml.png)

### <a name="1-add-a-3d-model"></a>1. Добавление трехмерной модели

Ресурсы должны добавляться в проект с **SceneKitAsset** действие при сборке.

![Активов SceneKit в проекте](images/scene-assets.png)


### <a name="2-configure-the-view"></a>2. Настройка представления

В контроллере представления `ViewDidLoad` метода загрузки ресурса сцены и задайте `Scene` свойство представления:

```csharp
ARSCNView SceneView = (View as ARSCNView);

// Create a new scene
var scene = SCNScene.FromFile("art.scnassets/ship");

// Set the scene to the view
SceneView.Scene = scene;
```

### <a name="3-optionally-implement-a-session-delegate"></a>3. При необходимости реализации делегата сеанса

Несмотря на то, что не требуется для простых случаях, реализация сеанса делегат может быть полезным при отладке состояние сеанса ARKit (и в реальных приложениях, обратная связь для пользователя). Создание простого делегата с помощью следующего кода:

```csharp
public class SessionDelegate : ARSessionDelegate
{
  public SessionDelegate() {}
  public override void CameraDidChangeTrackingState(ARSession session, ARCamera camera)
  {
    Console.WriteLine("{0} {1}", camera.TrackingState, camera.TrackingStateReason);
  }
}
```

Назначить делегата в в `ViewDidLoad` метод:

```csharp
// Track changes to the session
SceneView.Session.Delegate = new SessionDelegate();
```

### <a name="4-position-the-3d-model-in-the-world"></a>4. Положение в мире трехмерной модели

В `ViewWillAppear`, следующий код устанавливает сеанс ARKit и задает положение трехмерной модели в пространстве относительно камеры устройства:

```csharp
// Create a session configuration
var configuration = new ARWorldTrackingConfiguration {
  PlaneDetection = ARPlaneDetection.Horizontal,
  LightEstimationEnabled = true
};

// Run the view's session
SceneView.Session.Run(configuration, ARSessionRunOptions.ResetTracking);

// Find the ship and position it just in front of the camera
var ship = SceneView.Scene.RootNode.FindChildNode("ship", true);

ship.Position = new SCNVector3(2f, -2f, -9f);
```

Каждый раз, запустить или возобновлении приложения камеры будет располагаться трехмерной модели. Когда модель находится, перемещение камеры и посмотрите, как ARKit сохраняют находится модель.

### <a name="5-pause-the-augmented-reality-session"></a>5. Приостановка сеанса дополненная реальность

Рекомендуется приостановить сеанс ARKit, когда контроллер представления не отображается (в `ViewWillDisappear` метод:

```csharp
SceneView.Session.Pause();
```

## <a name="summary"></a>Сводка

Приведенные выше результаты кода в простом приложении ARKit. Более сложные примеры ожидать контроллера представления хостом сеанса режиме дополненной реальности для реализации `IARSCNViewDelegate`, и реализовать дополнительные методы.

ARKit предоставляет множество других более сложные функции, такие как surface отслеживания и взаимодействия с пользователем. См. в разделе [UrhoSharp Демонстрация](urhosharp.md) пример объединения ARKit отслеживания с UrhoSharp.


## <a name="related-links"></a>Связанные ссылки

- [Дополненная реальность (Apple)](https://developer.apple.com/arkit/)
- [С помощью ARKit с UrhoSharp](urhosharp.md)
- [Пример простой ARKit (Jet)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitSample/)
- [Размещение объектов ARKit (пример)](https://developer.xamarin.com/samples/monotouch/ios11/ARKitPlacingObjects/)
- [Знакомство с ARKit - режиме дополненной реальности для iOS (WWDC) (видео)](https://developer.apple.com/videos/play/wwdc2017/602/)
