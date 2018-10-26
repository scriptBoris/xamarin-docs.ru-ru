---
title: Анимация с CCAction
description: Класс CCAction упрощает добавление анимации CocosSharp игры. Эти анимации можно использовать для реализации функциональных возможностей или добавления польский.
ms.prod: xamarin
ms.assetid: 74DBD02A-6F10-4104-A61B-08CB49B733FB
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: c486bb2e78579360e0f935219cd82958fedee34b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120974"
---
# <a name="animating-with-ccaction"></a>Анимация с CCAction

_Класс CCAction упрощает добавление анимации CocosSharp игры. Эти анимации можно использовать для реализации функциональных возможностей или добавления польский._

`CCAction` является базовым классом, который может использоваться для анимации объектов CocosSharp. В этом руководстве описывается встроенная `CCAction` реализации для общих задач, таких как расположение, масштаба и вращение. Он также рассматривает способы создания пользовательских реализаций путем наследования от `CCAction`.

В этом руководстве используется проект с именем **ActionProject** которой [можно загрузить здесь](https://developer.xamarin.com/samples/mobile/CCAction). В этом руководстве используется `CCDrawNode` класс, который рассматривается в [Рисование геометрия с CCDrawNode](~/graphics-games/cocossharp/ccdrawnode.md) руководства.


## <a name="running-the-actionproject"></a>Под управлением ActionProject

**ActionProject** — это CocosSharp решение, которое может быть построен для iOS и Android. Он служит только в качестве образца кода, как использовать `CCAction` класса и как в режиме реального времени демонстрацию общих `CCAction` реализаций.

При запуске, ActionProject отображает три `CCLabel` экземпляры в левой части экрана и визуального объекта, нарисованными два `CCDrawNode` экземпляров для просмотра различных действий:

![](ccaction-images/image1.png "ActionProject отображает три экземпляра CCLabel в левой части экрана и визуального объекта, нарисованными два экземпляра CCDrawNode для просмотра различных действий")

В метках слева указывается, какой тип `CCAction` создается, когда при нажатии кнопки на экране. По умолчанию **позиции** выбрано значение, в результате чего `CCMove` действие создается и применяется для красного кружка:

![](ccaction-images/image2.gif "При выборе значения позиции, в результате чего выполняется действие CCMove создается и применяется для красного кружка")

Щелкнув метки слева изменения какого рода `CCAction` выполняется в элемент управления circle. Например, при щелчке **позиции** метки будут последовательно отображаться различные значения, которые могут быть изменены:

![](ccaction-images/image3.gif "Щелкнув метка позиции будут последовательно отображаться различные значения, которые могут быть изменены")

## <a name="common-variable-changing-ccaction-classes"></a>Общие классы CCAction изменение переменной

**ActionProject** использует следующие `CCAction`-наследования классов, которые являются частью CocosSharp:

 - `CCMoveTo` — Изменяет `CCNode` экземпляра `Position` свойство
 - `CCScaleTo` — Изменяет `CCNode` экземпляра `Scale` свойство
 - `CCRotateTo` — Изменяет `CCNode` экземпляра `Rotation` свойство

В этом руководстве ссылается на эти действия, как *изменение переменной*, что означает, что они непосредственно влияют на переменной `CCNode` , они добавляются. Других типов действий, называются *плавности* действия, которые рассматриваются далее в этом руководстве.

**ActionProject** демонстрирует, что цель этих действий, чтобы изменить переменную со временем. В частности, они `CCActions` конструкторов принимают два аргумента: продолжительность времени, чтобы получить и значение для назначения. Следующий фрагмент кода показывает, как создаются три типа действий:


```csharp
switch (VariableOptions [currentVariableIndex])
{
    case "Position":
        coreAction = new CCMoveTo(timeToTake, touch.Location);

        break;
    case "Scale":
        var distance = CCPoint.Distance (touch.Location, drawNodeRoot.Position);
        var desiredScale = distance / DefaultCircleRadius;
        coreAction = new CCScaleTo(timeToTake, desiredScale);

        break;
    case "Rotation":
        float differenceY = touch.Location.Y - drawNodeRoot.PositionY;
        float differenceX = touch.Location.X - drawNodeRoot.PositionX;

        float angleInDegrees = -1 * CCMathHelper.ToDegrees(
            (float)System.Math.Atan2(differenceY, differenceX));

        coreAction = new CCRotateTo (timeToTake, angleInDegrees);

        break; 
...
}
```

После создания действия, он добавляется CCNode следующим образом:


```csharp
nodeToAddTo.AddAction (coreAction); 
```

`AddAction` Запускает `CCAction` поведение экземпляра и он автоматически выполнит его логики-после фрейма до ее завершения.

Каждый из типов, перечисленных выше заканчивается символом слова *для* означающее `CCAction` изменит `CCNode` таким образом, значение аргумента, который представляет конечное состояние после завершения действия. Например, создание `CCMoveTo` с позицией X = 100, а Y = 200 приводит `CCNode` экземпляра `Position` , равным X = 100, Y = 200 в конце времени, указанного, независимо от `CCNode` экземпляр начальному расположению.

Каждый класс «To» также имеет версию «,», которое добавляет значение аргумента в текущее значение на `CCNode`. Например, создание `CCMoveBy` с позицией X = 100, а Y = 200 приведет к `CCNode` экземпляра, перемещаемого к единицам справа 100 и более 200 единиц начиная с позиции, на время запуска действия.


## <a name="easing-actions"></a>Действия для реалистичной анимации

По умолчанию будет выполнять действия переменной изменяющие *линейной интерполяции* — действие будет перейти к нужное значение с постоянной скоростью. Если интерполяцией *позиции* линейно, объекта перемещения будет немедленно начать останавливать движение в начале и конце действие и его скорости останется постоянной, а действие выполняется. 

Нелинейная интерполяции менее резать глаз и добавляет элемент польская, поэтому CocosSharp предлагает широкий набор действий, которые можно использовать для изменения действия изменяющие переменной для реалистичной анимации.

В **ActionProject** пример, мы можем переключиться между этими типами плавности действий, щелкнув второй метки (который по умолчанию **<None>**):

![](ccaction-images/image4.gif "Можно переключаться между этими типами плавности действий, щелкнув второй метки")

Плавности действия — удобно, так как они не привязаны к какие-то действия переменной параметр. Это означает, что одно и то же плавности действие может использоваться для назначения позиции, поворота, масштабирования или настраиваемые действия (как показано далее в этом руководстве).

Действия переменной параметр wrap плавности действия (до тех пор, пока параметр переменной действие наследует от `CCFiniteTimeAction`), принимая действие параметра переменной как аргумента в их конструкторов.

Например, если метки **позиции**, **CCEaseElastic**, то следующий код будет выполняться при обнаружении сенсорного ввода (Обратите внимание, что код включен для выделения соответствующих строк):


```csharp
CCFiniteTimeAction coreAction = null; 
...
coreAction = new CCMoveTo(timeToTake, touch.Location); 
...
CCAction easing = null; 
...
easing = new CCEaseSineOut (coreAction); 
...
nodeToAddTo.AddAction (easing); 
```

Как показано приложение, точное же плавности могут быть применены к другим действиям параметр переменной например `CCRotateTo`:

![](ccaction-images/image5.gif "Точное же плавности могут применяться другие действия параметра переменной, например CCRotateTo")


## <a name="easing-in-out-and-inout"></a>В, а также и InOut для реалистичной анимации

Все действия плавности имеют `In`, `Out`, или `InOut` добавляется к типу плавности. Эти термины относятся к при применении плавности: `In` означает, что для реалистичной анимации будут применены в начале, `Out` означает, что в конце и `InOut` означает, что и в начале и конце.

`In` Плавности действие будет влиять на способ переменной применяется на протяжении всей интерполяции (как в начале и конце), но обычно узнаваемого характеристики плавности действие будет выполняться в начале. Аналогичным образом `Out` плавности действия характеризуются их поведения в конце интерполяции. Например `CCEaseBounceOut` приведет к объекта прыгающий в конце действия.


### <a name="out"></a>Out

`Out` Обычно для реалистичной анимации применяется наиболее заметных изменений в конце интерполяции. Например `CCEaseExponentialOut` приведет к снижению скорости изменения изменение переменной мере приближения к целевым значением:

![](ccaction-images/image6.gif "Мере приближения к целевым значением CCEaseExponentialOut приведет к снижению скорости изменения изменение переменной")


### <a name="in"></a>Увеличение

`In` Обычно для реалистичной анимации применяется наиболее значительное изменение в начале интерполяции. Например `CCEaseExponentialIn` переместит медленнее в начале действия:

![](ccaction-images/image7.gif "CCEaseExponentialIn переместит медленнее в начале действия")


### <a name="inout"></a>InOut

`InOut` обычно относится наиболее заметных отличий, как в начале и конце. `InOut` Обычно симметричен плавности. Например `CCEaseExponentialInOut` переместит медленно в начале и в конце операции:

![](ccaction-images/image8.gif "В начале и в конце операции CCEaseExponentialInOut переместит медленно")


## <a name="implementing-a-custom-ccaction"></a>Реализация пользовательского CCAction

Все классы, обсуждавшиеся до сих включаются в CocosSharp обеспечивают общие функциональные возможности. Custom `CCAction` реализации можно обеспечить дополнительную гибкость. Например `CCAction` для контроля заполненного отношение в панели качества может использоваться, чтобы панели качества плавно увеличивается каждый раз, когда пользователь получает интерфейс.

`CCAction` реализации обычно требуется два класса:

 - `CCFiniteTimeAction` Реализация — класс конечного времени действия отвечает за Запуск действия. Это класс, который создается и либо непосредственно добавлять в `CCNode` или с действием плавности. Он должен создать экземпляр и вернуть `CCFiniteTimeActionState`, который будет выполнять обновления.
 - `CCFiniteTimeActionState` Реализация — класс конечного времени действия состояния отвечает за обновление переменных, используемых в действии. Он должен реализовать функции обновления, которая назначает значение для целевого объекта в соответствии с значением времени. Этот класс не используется явно за пределами `CCFiniteTimeAction` создает его. Он просто работает «за кулисами».

**ActionProject** обеспечивает настраиваемый `CCFiniteTimeAction` реализация вызван `LineWidthAction,` котором можно настроить ширину нижнего желтые линии, рисуемой поверх красного кружка. Обратите внимание, что для создания экземпляра и вернуть его единственная задача `LineWidthState` экземпляр:


```csharp
public class LineWidthAction : CCFiniteTimeAction
{
    float endWidth;

    public LineWidthAction (float duration, float width) : base(duration)
    {
        endWidth = width;
    }

    public override CCFiniteTimeAction Reverse ()
    {
        throw new NotImplementedException ();
    }

    protected override CCActionState StartAction (CCNode target)
    {
        return new LineWidthState (this, target, endWidth);
    }
}
```

Как упоминалось выше, `LineWidthState` выполняет работу назначения строки `Width` свойства в соответствии с какой объем `time` прошло:


```csharp
public class LineWidthState : CCFiniteTimeActionState
{
    float deltaWidth;
    float startWidth;

    LineNode castedTarget;

    public LineWidthState(LineWidthAction action, CCNode target, float endWidth) : base(action, target)
    {
        castedTarget = target as LineNode;

        if (castedTarget == null)
        {
            throw new InvalidOperationException ("The argument target must be a LineNode");
        }

        startWidth = castedTarget.Width;
        deltaWidth = endWidth - startWidth;
    }

    public override void Update (float time)
    {
        castedTarget.Width = startWidth + deltaWidth * time;
    }
} 
```

LineWidthAction могут объединяться с действием плавности, чтобы изменить ширину линии различными способами, как показано в следующем:

![](ccaction-images/image9.gif "LineWidthAction могут сочетаться с действием плавности, чтобы изменить ширину линии различными способами, как показано на данной анимации")


### <a name="interpolation-and-the-update-method"></a>Интерполяция и метод обновления

Только логики, помимо хранения значений в классах выше, живет в `LineWidthState.Update` метод. `startWidth` Переменная хранит целевой ширины `LineNode` в начале действия и `deltaWidth` переменная хранит сколько изменится во время выполнения действия.

Путем замены `time` переменной со значением 0, мы видим, что целевой объект `LineNode` будет составлять его начальной позиции:


```csharp
castedTarget.Width = startWidth + deltaWidth * 0; 
```

Аналогичным образом, мы видим, что целевой объект `LineNode` будет по назначению, подставляя переменной времени со значением 1:


```csharp
castedTarget.Width = startWidth + deltaWidth * 1; 
```

`time` Значение обычно будет в диапазоне от 0 до 1 - но не всегда - и `Update` реализаций не должен предполагать эти границы. Некоторые методы плавности (такие как `CCEaseBackIn` и `CCEaseBackOut`) предоставляет значение времени, выходящие за пределы диапазона от 0 до 1.


## <a name="conclusion"></a>Заключение

Интерполяции и замедление являются важной частью создания великолепные игры, особенно в том случае, при создании пользовательских интерфейсов. В этом руководстве описывается, как использовать `CCActions` для интерполяции стандартных значений, таких как положения и поворота, а также пользовательские значения. `LineWidthState` И `LineWidthAction` классы демонстрируются способы реализации настраиваемого действия.

## <a name="related-links"></a>Связанные ссылки

- [CCAction](https://developer.xamarin.com/api/type/CocosSharp.CCAction)
- [CCMoveTo](https://developer.xamarin.com/api/type/CocosSharp.CCMoveTo)
- [CCScaleTo](https://developer.xamarin.com/api/type/CocosSharp.CCScaleTo)
- [CCRotateTo](https://developer.xamarin.com/api/type/CocosSharp.CCRotateTo)
- [CCDrawNode](https://developer.xamarin.com/api/type/CocosSharp.CCDrawNode)
- [Полный пример](https://developer.xamarin.com/samples/mobile/CCAction/)
