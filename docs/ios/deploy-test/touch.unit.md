---
title: Модульное тестирование приложений Xamarin.iOS
description: Этот документ содержит общие сведения о модульном тестировании приложения Xamarin.iOS. Он описывает, как создать проект модульного тестирования, а также писать и запускать тесты.
ms.prod: xamarin
ms.assetid: BD959779-3239-79B6-5289-3A9ECDFBD973
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: ce2b452d50222ac3561dab5b76915b7ae634934b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34785467"
---
# <a name="unit-testing-xamarinios-apps"></a>Модульное тестирование приложений Xamarin.iOS

В этом документе описывается создание модульных тестов для проектов Xamarin.iOS.
Модульное тестирование с помощью Xamarin.iOS осуществляется с помощью платформы Touch.Unit, в состав которой входит средство запуска тестов iOS и измененная версия платформы NUnit, которая называется [Touch.Unit](https://github.com/xamarin/Touch.Unit) и предоставляет знакомый набор API-интерфейсов для написания модульных тестов.

## <a name="setting-up-a-test-project"></a>Настройка тестового проекта

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Чтобы настроить платформу модульного тестирования для проекта, необходимо лишь добавить в решение проект типа **Проект модульных тестов iOS**. Для этого щелкните решение правой кнопкой мыши и выберите **Добавить > Добавить новый проект**. В списке выберите **iOS > Тесты > Unified API > Проект модульных тестов iOS** (можно выбрать язык C# или F#).

![](touch.unit-images/00.png "Выбор языка C# или F#")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Чтобы настроить платформу модульного тестирования для проекта, необходимо лишь добавить в решение проект типа **Проект модульных тестов iOS**. Для этого щелкните решение правой кнопкой мыши и выберите **Добавить > Новый проект**. В списке выберите **Visual C# > iOS > Приложение модульного тестирования (iOS)**.

![](touch.unit-images/00a.png "Приложение модульного тестирования (iOS)")

-----

После этого будет создан базовый проект, содержащий основное средство выполнения тестов и ссылающийся на новую сборку MonoTouch.NUnitLite. Проект будет выглядеть следующим образом:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

![](touch.unit-images/01.png "Проект в обозревателе решений")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

![](touch.unit-images/01a.png "Проект в обозревателе решений")

-----

Класс `AppDelegate.cs` содержит средство выполнения тестов и выглядит следующим образом:

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
        UIWindow window;
        TouchRunner runner;

        public override bool FinishedLaunching (UIApplication app, NSDictionary options)
        {
                // create a new window instance based on the screen size
                window = new UIWindow (UIScreen.MainScreen.Bounds);
                runner = new TouchRunner (window);

                // register every tests included in the main application/assembly
                runner.Add (System.Reflection.Assembly.GetExecutingAssembly ());

                window.RootViewController = new UINavigationController (runner.GetViewController ());

                // make the window visible
                window.MakeKeyAndVisible ();

                return true;
        }
}
```

## <a name="writing-some-tests"></a>Написание некоторых тестов

Теперь, когда имеется базовая оболочка, следует написать первый набор тестов.

Тесты написаны путем создания классов с примененным к ним атрибутом `[TestFixture]`. В каждом классе TestFixture атрибут `[Test]` следует применить к каждому методу, который должен вызывать средство выполнения тестов. Фактические средства тестирования могут существовать в любом файле в проекте тестирования.

Чтобы быстро приступить к работе, выберите **Добавить/Добавить новый файл** и выберите **UnitTests** в группе Xamarin.iOS. После этого будет добавлен структурный файл, содержащий один пройденный тест, один непройденный тест и один пропущенный тест. Файл выглядит следующим образом:

```csharp
using System;
using NUnit.Framework;

namespace Fixtures {

        [TestFixture]
        public class Tests {

                [Test]
                public void Pass ()
                {
                        Assert.True (true);
                }

                [Test]
                public void Fail ()
                {
                        Assert.False (true);
                }

                [Test]
                [Ignore ("another time")]
                public void Ignore ()
                {
                        Assert.True (false);
                }
        }
}
```

## <a name="running-your-tests"></a>Выполнение тестов

Для запуска проекта в решении щелкните его правой кнопкой мыши и выберите пункт **Отладка элемента** или **Запустить элемент**.

Средство выполнения тестов позволяет просмотреть, какие тесты регистрируются, и по отдельности выбрать, какие тесты могут выполняться.

[![](touch.unit-images/02.png "Список зарегистрированных тестов")](touch.unit-images/02.png#lightbox) 

[![](touch.unit-images/03.png "Отдельный тест")](touch.unit-images/03.png#lightbox) 

[![](touch.unit-images/04.png "Результаты выполнения")](touch.unit-images/04.png#lightbox)

Можно выполнить отдельное средство тестирования, выбрав его из вложенных представлений, или выполнить все тесты, выбрав "Выполнить все". При выполнении теста по умолчанию он должен содержать один пройденный тест, один непройденный тест и один пропущенный тест. Отчет будет выглядеть следующим образом. Можно перейти непосредственно к непройденному тесту и изучить дополнительные сведения о сбое:

[![](touch.unit-images/05.png "Пример отчета")](touch.unit-images/05.png#lightbox) [![](touch.unit-images/05.png "Пример отчета")](touch.unit-images/05.png#lightbox) [![](touch.unit-images/05.png "Пример отчета")](touch.unit-images/05.png#lightbox)

Можно также обратиться к окну выходных данных приложения в интегрированной среде разработки, чтобы увидеть, какие тесты выполняются, и их текущее состояние.

## <a name="writing-new-tests"></a>Написание новых тестов

NUnitLite является модификацией NUnit, которая называется проектом [Touch.Unit](https://github.com/xamarin/Touch.Unit). Это облегченная платформа тестирования для .NET, созданная на базе [NUnit](http://nunit.com/) и содержащая поднабор ее компонентов.
Она использует минимальный объем ресурсов и подходит для платформ с ограниченными ресурсами, таким, как те, которые используются при разработке внедряемых решений и решений для мобильных устройств. Можно [просмотреть API NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/), доступный в Xamarin.iOS. С помощью базовой структуры, которая содержится в шаблоне модульного теста, вы получаете методы [класса Assert](https://developer.xamarin.com/api/type/NUnit.Framework.Assert/), которые являются основной точкой входа.

Помимо методов класса assert, функциональные возможности модульного тестирования разбиты на следующие пространства имен, которые являются частью NUnitLite:

-   [NUnit.Framework](https://developer.xamarin.com/api/namespace/NUnit.Framework/)
-   [NUnit.Constraints](https://developer.xamarin.com/api/namespace/NUnit.Framework.Constraints/)
-   [NUnitLite](https://developer.xamarin.com/api/namespace/NUnitLite/)
-   [NUniteLite.Runner](https://developer.xamarin.com/api/namespace/NUnitLite.Runner/)


Более подробно средство выполнения модульных тестов для Xamarin.iOS описано здесь:

-   [NUnit.UI.TouchRunner](https://developer.xamarin.com/api/type/NUnit.UI.TouchRunner/)
