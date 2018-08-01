---
title: Анимация с CCAction
description: Класс CCAction упрощает добавление анимации CocosSharp игры. Эти анимации можно использовать для реализации функций или добавления польского языка.
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: charlespetzold
ms.author: chape
ms.date: 03/24/2017
ms.openlocfilehash: b6209816f741423f40945a0fe4391fe921cb35de
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921791"
---
# <a name="animating-with-ccaction"></a>Анимация с CCAction

_Класс CCAction упрощает добавление анимации CocosSharp игры. Эти анимации можно использовать для реализации функций или добавления польского языка._

`CCAction` является базовым классом, который можно использовать для анимации объектов CocosSharp. В настоящем руководстве описывается встроенные `CCAction` реализации для общих задач, например положение, масштабирования и поворота. На ней также рассматривается создание пользовательских реализаций путем наследования от `CCAction`.

В этом руководстве используется проект с именем **ActionProject** которой [можно загрузить здесь](https://developer.xamarin.com/samples/mobile/CCAction). В этом руководстве используется `CCDrawNode` класс, который рассматривается в [Рисование геометрических с CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md) руководства.


## <a name="running-the-actionproject"></a>Запуск ActionProject

**ActionProject** — CocosSharp решение, которое может быть построен для iOS и Android. Он выполняет и как образец кода по использованию `CCAction` класса и как в режиме реального времени демонстрации общих `CCAction` реализации.

При запуске, ActionProject отображает три `CCLabel` экземпляров в левой части экрана и визуальный объект, нарисованная с двумя `CCDrawNode` экземпляров для просмотра различных действий:

![](ccaction-images/image1.png "ActionProject отображает три экземпляра CCLabel в левой части экрана и визуальный объект, нарисованная с двумя экземплярами CCDrawNode для просмотра различных действий")

Указать метки в левой части экрана выберите тип `CCAction` создается во время касания на экране. По умолчанию **позиции** выбрано значение, в результате чего `CCMove` действие и его применения на красный кружок:

![](ccaction-images/image2.gif "При выборе значения позиции, в результате чего выполняется CCMove действия и его применения на красный круг")

После щелчка метки слева изменяется какой тип `CCAction` над круга. Например, если щелкнуть **позиции** метка будет циклическое переключение между различными значениями, которые могут быть изменены:

![](ccaction-images/image3.gif "Щелкнув метка позиции будет циклическое переключение между различными значениями, которые могут быть изменены")

## <a name="common-variable-changing-ccaction-classes"></a>Общие классы CCAction изменение переменной

**ActionProject** использует следующие `CCAction`-производные классы, которые являются частью CocosSharp:

 - `CCMoveTo` — Изменяет `CCNode` экземпляра `Position` свойство
 - `CCScaleTo` — Изменяет `CCNode` экземпляра `Scale` свойство
 - `CCRotateTo` — Изменяет `CCNode` экземпляра `Rotation` свойство

Это руководство относится к этим действиям, как *изменение переменной*, означающее напрямую влияет переменной `CCNode` , они добавляются. Другие типы действий, называются *плавности* действия, которые рассматриваются далее в этом руководстве.

**ActionProject** демонстрирует, что назначение этих действий, попытка изменить переменную со временем. В частности, они `CCActions` конструкторы принимают два аргумента: продолжительность времени, чтобы получить и присваиваемое значение. Следующий фрагмент кода показывает создание трех типов действий:


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

После создания действия оно добавляется к CCNode следующим образом:


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` Запускает `CCAction` поведение экземпляра и он автоматически выполнит его логики-после фрейма до ее завершения.

Каждый из типов, перечисленных выше заканчивается словом *для* означающее `CCAction` изменит `CCNode` , чтобы значение аргумента представляет конечное состояние по завершении действия. Например, создание `CCMoveTo` с позицией X = 100, а Y = 200 приводит `CCNode` экземпляра `Position` устанавливается X = 100, Y = 200 в конце времени указано, независимо от того, `CCNode` экземпляр начальному расположению.

Каждый класс «To» также имеет «По» версии, добавьте значение аргумента в текущее значение на `CCNode`. Например, создание `CCMoveBy` с позицией X = 100, а Y = 200 приведет к `CCNode` экземпляра, перемещаемого единиц вправо 100 и 200 единицы, начиная с позиции, он находился запуска действия.


## <a name="easing-actions"></a>Упрощение действия

По умолчанию будет выполнять действия, изменяющие переменной *линейную интерполяцию* — действие будет перейти к нужное значение с постоянной скоростью. Если интерполяция *позиции* линейно, немедленно запуск и остановка, перемещение в начале и в конце операции перемещения объекта и его скорость останется постоянной, при выполнении действия. 

Нелинейная интерполяции менее резать глаз и добавляет элемент польский, поэтому CocosSharp предоставляет множество плавности действия, которые можно использовать для изменения действия Изменение переменной.

В **ActionProject** образца, мы можем переключиться между этими типами плавности действия, щелкнув на второй метки (который по умолчанию **<None>**):

![](ccaction-images/image4.gif "Можно переключаться между этими типами плавности действия, щелкнув на второй метки")

Плавности действий, особенно широкими возможностями, так как они не привязаны к никаких конкретных действий параметр переменной. Это означает, что плавности действие может использоваться для назначения позиции, поворота, масштабирования или настраиваемых действий (как показано далее в этом руководстве).

Действия переменной параметр wrap плавности действия (при условии, что параметр переменной действие наследует от `CCFiniteTimeAction`), принимая действие параметра переменной как аргумента их конструкторы.

Например, если метки в **позиции**, **CCEaseElastic**, то следующий код будет выполняться при обнаружении касание (Обратите внимание, что код включен для выделения соответствующих строк):


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

Как показано в приложении, точное же плавности может применяться к другие действия переменной параметр например `CCRotateTo`:

![](ccaction-images/image5.gif "Точное же плавности может применяться к другим переменной параметр действия, такие как CCRotateTo")


## <a name="easing-in-out-and-inout"></a>Упрощение In, Out и InOut

Все действия плавности `In`, `Out`, или `InOut` добавляется в тип реалистичной анимации. Следующие термины относятся применения реалистичной анимации: `In` означает реалистичной анимации будут применены в начале, `Out` означает в конце, и `InOut` означает как в начале и в конце.

`In` Плавности действия влияют на способ переменной применяется ко всем всей интерполяции (как в начале и конце), но обычно наиболее распознаваемым характеристики плавности действие будет иметь место в начале. Аналогичным образом `Out` плавности действия характеризуются их поведения в конце интерполяции. Например `CCEaseBounceOut` приведет к перебрасываются в конце действие объекта.


### <a name="out"></a>Out

`Out` Обычно замедление применяет изменения наиболее заметны в конце интерполяции. Например `CCEaseExponentialOut` приведет к снижению скорости изменения изменение переменной мере приближения целевое значение:

![](ccaction-images/image6.gif "CCEaseExponentialOut приведет к снижению скорости изменения изменение переменной мере приближения целевое значение")


### <a name="in"></a>Увеличение

`In` Обычно замедление применяется наиболее значительное изменение в начале интерполяции. Например `CCEaseExponentialIn` будет медленнее, переместить в начало действия:

![](ccaction-images/image7.gif "CCEaseExponentialIn переместит медленнее в начале действия")


### <a name="inout"></a>InOut

`InOut` обычно применяется наиболее заметны изменения как в начале и в конце. `InOut` Обычно симметричен реалистичной анимации. Например `CCEaseExponentialInOut` переместит медленно в начале и в конце операции:

![](ccaction-images/image8.gif "В начале и в конце операции CCEaseExponentialInOut переместит медленно")


## <a name="implementing-a-custom-ccaction"></a>Реализация пользовательского CCAction

Все классы, которые мы обсуждали до сих включаются в CocosSharp обеспечивают общие функциональные возможности. Настраиваемый `CCAction` реализации можно обеспечить дополнительную гибкость. Например `CCAction` для контроля заполненный отношение полосы качества можно использовать, чтобы строке качества плавно увеличивается каждый раз, когда пользователь получает качества.

`CCAction` реализации обычно требуются два класса:

 - `CCFiniteTimeAction` Реализация — класс времени окончания действия отвечает за запуск действие. Класс, который создается, и либо непосредственно добавлять в `CCNode` или действию, реалистичной анимации. Он должен создать экземпляр и вернуть `CCFiniteTimeActionState`, который будет выполнять обновления.
 - `CCFiniteTimeActionState` Реализация — класс состояние конечное время действия отвечает за обновление переменных, используемых в действии. Он должен реализовать функции обновления, которая назначает значение для целевого объекта в соответствии со значением времени. Этот класс не содержит явной ссылки за пределами `CCFiniteTimeAction` его создает. Он просто работает «за кулисами».

**ActionProject** предоставляет пользовательское `CCFiniteTimeAction` реализация вызван `LineWidthAction,` , используемое для ширину желтая линия, проведенная поверх красный кружок. Обратите внимание, что для создания экземпляра и вернуть его только задания `LineWidthState` экземпляр:


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

Как было сказано выше, `LineWidthState` работы назначения в строке не `Width` свойству в соответствии с тем, сколько `time` достиг:


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

LineWidthAction могут объединяться с действием реалистичной анимации, чтобы изменить толщину линии различными способами, как показано в следующем анимации:

![](ccaction-images/image9.gif "LineWidthAction могут объединяться с действием реалистичной анимации, чтобы изменить толщину линии различными способами, как показано в данной анимации")


### <a name="interpolation-and-the-update-method"></a>Интерполяция и метод обновления

Только логики, помимо хранения значений в классах выше живет в `LineWidthState.Update` метод. `startWidth` Переменная хранит целевой ширины `LineNode` в начале этого действия и `deltaWidth` переменная хранит сколько изменится в ходе действия.

Путем замены `time` переменной со значением 0, можно видеть, что целевой объект `LineNode` будут в его начальное положение:


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

Аналогичным образом, можно видеть, что целевой объект `LineNode` будет по назначению, заменив переменной времени со значением 1:


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

`time` Значение обычно будет между 0 и 1 — но не всегда - и `Update` реализации не следует рассчитывать на такие границы. Некоторых плавности методы (такие как `CCEaseBackIn` и `CCEaseBackOut`) будет предоставлять значение времени вне диапазона 0 до 1.


## <a name="conclusion"></a>Заключение

Интерполяции и упрощение являются важной частью создания профессиональный игры, особенно при создании пользовательских интерфейсов. В этом руководстве описывается использование `CCActions` для интерполяции стандартных значений, таких как положения и поворота, а также пользовательские значения. `LineWidthState` И `LineWidthAction` классов показывают, как реализовать пользовательское действие.

## <a name="related-links"></a>Связанные ссылки

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [Полный пример](https://developer.xamarin.com/samples/mobile/CCAction/)
