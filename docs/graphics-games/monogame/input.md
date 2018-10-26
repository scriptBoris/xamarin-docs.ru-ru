---
title: Сведения о Gamepad MonoGame
description: В этом документе описывается игровой, кросс платформенных класс для доступа к устройствам ввода в MonoGame. Он описывает, как для чтения ввода из игровой и примеры кода.
ms.prod: xamarin
ms.assetid: 1F71F3E8-2397-4C6A-8163-6731ECFB7E03
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: badd23ebb78e61e7d7650ff6d0973226359fd9d5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117113"
---
# <a name="monogame-gamepad-reference"></a>Сведения о Gamepad MonoGame

_Игровой представляет собой стандартный кросс платформенных класс для доступа к устройств ввода в MonoGame._

`GamePad` можно использовать для чтения ввода из устройства ввода на нескольких платформах MonoGame. В этом руководстве показано, как работать с классом GamePad. Так как каждый устройство ввода отличается по структуре и число кнопок, которые он предоставляет, это руководство содержит диаграмм, иллюстрирующих различные сопоставления устройств.

## <a name="gamepad-as-a-replacement-for-xbox360gamepad"></a>Игровой для замены Xbox360GamePad

Исходный XNA API, предоставляемые `Xbox360GamePad` класс для чтения входных данных из игровой контроллер на Xbox 360 или ПК. MonoGame заменила с `GamePad` класса, так как устройства Xbox 360 нельзя использовать на большинстве платформ MonoGame (например, iOS или на Xbox One). Несмотря на использование, изменение имени `GamePad` класс аналогичен `Xbox360GamePad` класса.

## <a name="reading-input-from-gamepad"></a>Чтение входных данных из GamePad

`GamePad` Класс предоставляет стандартный путь чтения входных данных на любой платформе MonoGame. Он предоставляет сведения через два метода:

- `GetState` — Возвращает текущее состояние кнопки, аналоговый фиксируется и d панель контроллера.
- `GetCapabilities` — Возвращает сведения о возможностях оборудования, такие как контроллер, имеет ли определенные кнопки, или поддерживает вибрации.

### <a name="example-moving-a-character"></a>Пример: Перемещение символ

В следующем коде показано, как палочка левой бегунок может использоваться для перемещения символа, задав его `XVelocity` и `YVelocity` свойства. Этот код предполагает, что `characterInstance` — это экземпляр объекта, который имеет `XVelocity` и `YVelocity` свойства:

```csharp
// In Update, or some code called every frame:
var gamePadState = GamePad.GetState(PlayerIndex.One);
// Use gamePadState to move the character
characterInstance.XVelocity = gamePadState.ThumbSticks.Left.X * characterInstance.MaxSpeed;
characterInstance.YVelocity = gamePadState.ThumbSticks.Left.Y * characterInstance.MaxSpeed;
```

### <a name="example-detecting-pushes"></a>Пример: Определение Push-уведомлений

`GamePadState` сведения о текущем состоянии контроллера, например, нажата ли определенные кнопки. Определенные действия, например символ перехода, потребовать проверки, если был отправлен кнопки (не была недоступна. последний кадр, но не работает этот кадр) или выпуска (был вниз последнего кадра, но не работу этого кадра). 

Для выполнения такого рода логики, локальные переменные, которые хранят предыдущего кадра `GamePadState` и текущего кадра `GamePadState` необходимо создать. В следующем примере показано, как хранить и использовать предыдущий кадр `GamePadState` для реализации перехода:

```csharp
// At class scope:
// Store the last frame's and this frame's GamePadStates.
// "new" them so that code doesn't have to perform null checks:
GamePadState lastFrameGamePadState = new GamePadState();
GamePadState currentGamePadState = new GamePadState();
protected override void Update(GameTime gameTime)
{
    // store off the last state before reading the new one:
    lastFrameGamePadState = currentGamePadState;
    currentGamePadState = GamePad.GetState(PlayerIndex.One);
    bool wasAButtonPushed = 
currentGamePadState.Buttons.A == ButtonState.Pressed
        && lastFrameGamePadState.Buttons.A == ButtonState.Released;
    if(wasAButtonPushed)
    {
        MakeCharacterJump();
    }
...
}
```

### <a name="example-checking-for-buttons"></a>Пример: Проверка для кнопок

`GetCapabilities` можно проверить наличие определенных оборудованием, таким как конкретной кнопки или аналогового палочка контроллера. Приведенный ниже показано, как проверить наличие кнопки B и Y на контроллере в игре, который требует наличия обеих кнопок:

```csharp
var capabilities = GamePad.GetCapabilities(PlayerIndex.One);
bool hasBButton = capabilities.HasBButton;
bool hasXButton = capabilities.HasXButton;
if(!hasBButton || !hasXButton)
{
    NotifyUserOfMissingButtons();
}
```

## <a name="ios"></a>iOS

приложения iOS поддерживает ввод беспроводной игровой контроллер.

> [!IMPORTANT]
> Пакеты NuGet для MonoGame 3.5 включает поддержку игровых устройств управления беспроводной сети. С помощью класса игровой на iOS требуется создание MonoGame 3.5 из источника, либо с помощью MonoGame 3.6 NuGet двоичные файлы. 

### <a name="ios-game-controller"></a>Контроллер игру iOS

`GamePad` Класс возвращает свойств, считанных из беспроводной контроллеров. Свойства в `GamePad` представлены в пакетах для стандартных iOS контроллеров устройств, как показано на следующей схеме:

![](input-images/image1.png "Свойства в игровой представлены в пакетах для стандартных iOS контроллеров устройств, как показано на этой диаграмме")

## <a name="apple-tv"></a>Apple TV

Apple TV игры можно использовать Siri Remote или игровые устройства управления беспроводной сети для входных данных.

### <a name="siri-remote"></a>Siri Remote

*Siri Remote* является собственного устройства ввода для Apple TV. Несмотря на то, что значения из удаленного Siri можно узнать с помощью событий (как показано в [контроллеры Siri Remote и контроллеры Bluetooth руководство](~/ios/tvos/platform/remote-bluetooth.md)), `GamePad` класс может возвратить значения из удаленного Siri.

Обратите внимание, что `GamePad` можно только ввода кнопку воспроизведения и touch область: 

![](input-images/image2.png "Обратите внимание, что GamePad можно только считывать входные данные из \"Воспроизведение\" touch поверхности")

С момента сенсорное устройство прочтите поверхности перемещения `DPad` свойства, значения выводятся с помощью перемещения `ButtonState` класса. Другими словами, значения доступны только как `ButtonState.Pressed` или `ButtonState.Released`, а не числовые значения или жесты.

### <a name="apple-tv-game-controller"></a>Контроллер Apple TV игры

Игровые устройства управления для Apple TV ведут себя одинаково для игровые устройства управления для приложений iOS. Дополнительные сведения см. в разделе [iOS разделе игра контроллера](#iOS_Game_Controller). 

## <a name="xbox-one"></a>Xbox One

Консоль Xbox One поддерживает чтение входных данных из контроллера Xbox One игр.

### <a name="xbox-one-game-controller"></a>Игровой контроллер Xbox One

Xbox One игровой контроллер является наиболее распространенных устройство ввода для Xbox One. `GamePad` Класс предоставляет входные значения из игровых устройств.

![](input-images/image3.png "Класс GamePad предоставляет входные значения из игровых устройств")

## <a name="summary"></a>Сводка

В этом руководстве представлены общие сведения о его MonoGame `GamePad` класса, как реализовать логику чтения входных данных и схемы общих `GamePad` реализаций.

## <a name="related-links"></a>Связанные ссылки

- [MonoGame GamePad](http://www.monogame.net/documentation/?page=T_Microsoft_Xna_Framework_Input_GamePad)
