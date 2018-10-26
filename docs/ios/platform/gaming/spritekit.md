---
title: SpriteKit в Xamarin.iOS
description: В этом документе описывается SpriteKit, платформы Apple двумерной графики, который интегрируется с SceneKit, включает в себя физики и анимации, включает в себя поддержку освещения и заливки и многое другое. SpriteKit можно использовать для создания 2D-игр.
ms.prod: xamarin
ms.assetid: 93971DAE-ED6B-48A8-8E61-15C0C79786BB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: ef1e9a98b76166f4ee5638d1ab9762896d1e3bc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50121650"
---
# <a name="spritekit-in-xamarinios"></a>SpriteKit в Xamarin.iOS

SpriteKit, платформы двумерной графики от Apple, имеет ряд интересных функций в iOS 8 и OS X Yosemite. К ним относятся интеграция с SceneKit, поддержка построителя текстуры, освещения, теней, ограничения, создания карты нормалей и физики усовершенствования. В частности новые функции физики сделать очень легко добавить реалистичные эффекты для игры.

## <a name="physics-bodies"></a>Физика тела

SpriteKit включает двухмерной физики твердое тело API. Каждый спрайт имеет тело связанные физики (`SKPhysicsBody`), определяющий свойства Физика таких как запоминающих устройств и трудностей, а также геометрии текста в мире физики.

## <a name="creating-a-physics-body-from-a-texture"></a>Создание текста физики из текстуры
SpriteKit теперь поддерживает наследование от текстура тело физики спрайт. Это позволяет легко реализовать конфликтов, которые выглядят более естественным.

Например Обратите внимание, в следующий конфликт как «banana» и «monkey конфликтуют почти в рабочей области для каждого изображения:
 
![](spritekit-images/image13.png "«Banana» и «monkey конфликтуют почти в рабочей области для каждого изображения")

SpriteKit значительно упрощает создание таких телом физики можно с помощью одной строки кода. Просто вызовите `SKPhysicsBody.Create` текстуры и размером: спрайт. PhysicsBody = SKPhysicsBody.Create (спрайт. Текстуры, спрайт. Размер);

## <a name="alpha-threshold"></a>Пороговое значение альфа-канала

В дополнение к установке просто `PhysicsBody` свойство непосредственно к данной геометрии, производным от текстуры, приложение может задать и альфа-пороговое значение для управления как извлекаются геометрии. 

Пороговое значение альфа-определяет минимальное значение альфа-пиксель должны быть установлены для включения в полученный текст физики. Например следующий код приводит к телу физики немного отличается:

```chsarp
sprite.PhysicsBody = SKPhysicsBody.Create (sprite.Texture, 0.7f, sprite.Size);
```

Эффект настройки пороговое значение альфа-следующим образом fine-tunes предыдущий конфликт, таким образом, чтобы monkey попадает при несовместимости с банан:

![](spritekit-images/image14.png "Monkey попадает при несовместимости с банан")
 
## <a name="physics-fields"></a>Физика поля

Другим отличным добавлением к SpriteKit — новое поле физики поддержки. Они позволяют добавить такие поля vortex радиального тяжести полей и полей spring для именования несколько.

Физика поля создаются с помощью класса SKFieldNode, который добавляется в сцену подобно любому другому `SKNode`. Существует множество методов фабрики на `SKFieldNode` для создания различных физики полей. Можно создать поле spring путем вызова `SKFieldNode.CreateSpringField()`, поле радиального гравитации, вызвав `SKFieldNode.CreateRadialGravityField()`, и т. д.

`SKFieldNode` также имеет свойства, управляющие атрибуты полей, например силы поля, области поля и затухания поля сил.

## <a name="spring-field"></a>Поле Spring

Например следующий код создает поле spring и добавляет его в сцену:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateSpringField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 0.5f;
fieldNode.Region = new SKRegion(Frame.Size);
AddChild (fieldNode);
```

Затем добавьте спрайты и задавать их `PhysicsBody` свойства, чтобы поле физики повлияет спрайты, как делает следующий код, когда пользователь касается экрана:

```csharp
public override void TouchesBegan (NSSet touches, UIEvent evt)
{
    var touch = touches.AnyObject as UITouch;
    var pt = touch.LocationInNode (this);
    var node = SKSpriteNode.FromImageNamed ("TinyBanana");
    node.PhysicsBody = SKPhysicsBody.Create (node.Texture, node.Size);
    node.PhysicsBody.AffectedByGravity = false;
    node.PhysicsBody.AllowsRotation = true;
    node.PhysicsBody.Mass = 0.03f;
    node.Position = pt;
    AddChild (node);
}
```

В результате bananas колебания как spring вокруг узел поля:

![](spritekit-images/image15.png "Bananas колебания как spring вокруг узел поля")
 
## <a name="radial-gravity-field"></a>Поле радиального тяжести

Аналогично Добавление другое поле. Например следующий код создает радиального gravity поля:

```csharp
SKFieldNode fieldNode = SKFieldNode.CreateRadialGravityField ();
fieldNode.Enabled = true;
fieldNode.Position = new PointF (Size.Width / 2, Size.Height / 2);
fieldNode.Strength = 10.0f;
fieldNode.Falloff = 1.0f;
```

Это приводит к другому полю force, где Радиально извлеченные bananas о поле:

![](spritekit-images/image16.png "Bananas извлекаются Радиально вокруг поля")
