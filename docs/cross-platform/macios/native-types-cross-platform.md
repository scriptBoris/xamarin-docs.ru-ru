---
title: Работа с собственные типы в кросс платформенных приложений
description: В этой статье описывается использование новых iOS единой API, собственные типы (nint, nuint, nfloat) в приложении кросс платформенных, где код используется совместно с устройства без iOS, таким как Android или ОС Windows Phone.
ms.prod: xamarin
ms.assetid: B9C56C3B-E196-4ADA-A1DE-AC10D1001C2A
author: asb3993
ms.author: amburns
ms.date: 04/07/2016
ms.openlocfilehash: eb6979691eeef6dd7436d74fdfd501c747d9b3c6
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33918161"
---
# <a name="working-with-native-types-in-cross-platform-apps"></a>Работа с собственные типы в кросс платформенных приложений

_В этой статье описывается использование новых iOS единой API, собственные типы (nint, nuint, nfloat) в приложении кросс платформенных, где код используется совместно с устройства без iOS, таким как Android или ОС Windows Phone._


Собственные типы 64 типы работать с iOS и Mac API-интерфейсы. При создании общего кода, который выполняется в Windows или Android, также необходимо будет управлять преобразованием единой типов в обычных типов .NET, которые можно использовать совместно.

В этом документе описаны различные способы взаимодействия с API единой из общих общего кода.

## <a name="when-to-use-the-native-types"></a>Использование собственных типов

По-прежнему включить Xamarin.iOS и API-интерфейсы единой Xamarin.Mac `int`, `uint` и `float` типов данных, а также `RectangleF`, `SizeF` и `PointF` типов. Эти существующие типы данных должны продолжать использоваться в любом коде общего, между различными платформами. Новые типы данных в собственном использовать только при вызовах API-интерфейса Mac или iOS где поддержка поддержкой архитектуры типы являются обязательными.

В зависимости от общего доступа кода, может возникнуть где кроссплатформенного кода могут иметь дело с `nint`, `nuint` и `nfloat` типов данных. Например: библиотека, которая обрабатывает данные прямоугольника, который ранее с помощью преобразований `System.Drawing.RectangleF` для совместного использования функциональных возможностей между версиями Xamarin.iOS и Xamarin.Android приложения, будет необходимо обновить для обработки собственных типов в iOS.

Как обрабатываются эти изменения зависит от размера и сложности приложения и форме кода, управления доступом, который используется, как видно в разделах ниже.

## <a name="code-sharing-considerations"></a>Вопросы совместного использования кода

Как указано в [варианты совместного использования кода](~/cross-platform/app-fundamentals/code-sharing.md) документ, существует два основных способа совместное использование кода между проектами кросс платформенных: общие проекты и переносимой библиотеки классов. Какой из двух типов использовался, ограничит параметров, у нас есть при обработке собственные типы данных в кросс платформенного кода.

### <a name="portable-class-library-projects"></a>Переносимой библиотеки классов

Переносимая библиотека классов (PCL) можно ориентироваться на платформы, которую вы хотите поддерживать и использовать интерфейсы для предоставления функциональных возможностей платформы.

Поскольку тип проекта с PCL компилируется вплоть до `.DLL` и он не имеет смысла единой API-интерфейса, будет вынужден продолжать использовать существующие типы данных (`int`, `uint`, `float`) в PCL исходный код и тип приведения вызовы PCLs классы и методы в клиентские приложения. Пример.

```csharp
using NativePCL;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

### <a name="shared-projects"></a>Общие проекты

Тип проекта общих ресурсов позволяет упорядочивать исходный код в отдельном проекте, затем возвращает включается и компилируется в отдельных переднего плана с платформой приложения и использовать `#if` директивы компилятора как необходимые для управления требования к конкретной платформы.

Размер и сложность передней завершить мобильных приложений, которые используют общий код, а также размер и сложность кода является общей, необходимо принимать во внимание при выборе способа поддержки собственных данных типов кросс платформенных с Общий тип активов проекта.

На основе этих факторов, следующих типов решений может осуществляться с помощью `if __UNIFIED__ ... #endif` директивы компилятора для обработки единой API конкретные изменения в код.

#### <a name="using-duplicate-methods"></a>С помощью повторяющиеся методы

Рассмотрим в качестве примера библиотеку, которая выполняет преобразования на приведенном выше данные прямоугольника. Если библиотека содержит только один или два очень простые методы, можно создать повторяющиеся версии этих методов для Xamarin.iOS и Xamarin.Android. Пример:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #else
            public static float CalculateArea(RectangleF rect) {

                // Calculate area...
                return (rect.Width * rect.Height);

            }
        #endif
        #endregion
    }
}
```

В приведенном выше коде поскольку `CalculateArea` подпрограммы очень прост, мы использовать условную компиляцию и создать отдельные, но версия API единого метода. С другой стороны Если эта библиотека содержит множество процедур или несколько сложных подпрограммы, это решение не будет возможно, как должны выводиться синхронизируя все методы для изменения или исправления проблемы.

#### <a name="using-method-overloads"></a>С помощью метода перегрузки

В этом случае можно попробовать создать версию перегрузки методов, с помощью 32-разрядные типы данных так, чтобы они теперь `CGRect` как параметр или возвращаемое значение, преобразовать это значение для `RectangleF` (зная преобразование из `nfloat` для `float` преобразование с потерей данных) и вызвать исходную версию подпрограммы для выполнения фактическую работу. Пример:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
using CoreGraphics;
#endif

namespace NativeShared
{
    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        #if __UNIFIED__
            public static nfloat CalculateArea(CGRect rect) {

            // Call original routine to calculate area
            return (nfloat)CalculateArea((RectangleF)rect);

            }
        #endif

        public static float CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }

        #endregion
    }
}

```

Опять же это хорошее решение, при условии, что потери точности не влияет на результаты для определенных потребностей вашего приложения.

#### <a name="using-alias-directives"></a>Директивы псевдонима using

Для областей, где потеря точности важна, другим возможным решением является использование `using` директивы для создания псевдонима для машинного кода и CoreGraphics типов данных, включая следующий код в верхней части файлов общего исходного кода и преобразование любого требуется `int`, `uint` или `float` значения `nint`, `nuint` или `nfloat`:

```csharp
#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif
```

Чтобы становится наш пример кода:

```csharp
using System;
using System.Drawing;

#if __UNIFIED__
    // Mappings Unified CoreGraphic classes to MonoTouch classes
    using RectangleF = global::CoreGraphics.CGRect;
    using SizeF = global::CoreGraphics.CGSize;
    using PointF = global::CoreGraphics.CGPoint;
#else
    // Mappings Unified types to MonoTouch types
    using nfloat = global::System.Single;
    using nint = global::System.Int32;
    using nuint = global::System.UInt32;
#endif

namespace NativeShared
{

    public class Transformations
    {
        #region Constructors
        public Transformations ()
        {
        }
        #endregion

        #region Public Methods
        public static nfloat CalculateArea(RectangleF rect) {

            // Calculate area...
            return (rect.Width * rect.Height);

        }
        #endregion
    }
}
```

Обратите внимание, что здесь мы изменили `CalculateArea` возвращении метода `nfloat` вместо стандартной `float`. Это было сделано, чтобы не получаются ошибки компиляции, попытка _неявно_ преобразовать `nfloat` результат наших вычислений (так как оба значения умножаемое `nfloat`) в `float` возвращаемое значение.

Код при компиляции и выполнении на устройстве без единой API `using nfloat = global::System.Single;` сопоставляет `nfloat` для `Single` , которая неявно преобразует `float` позволяя много клиентского приложения для вызова `CalculateArea` метод без изменение.


#### <a name="using-type-conversions-in-the-front-end-app"></a>С помощью преобразования типов в приложении переднего плана

В случае, если приложения переднего плана только внести небольшое число вызовы кода общей библиотеки, еще одно решение может быть оставить без изменений библиотеки и приведение типов в приложении Xamarin.iOS или Xamarin.Mac при вызове подпрограммы существующий. Пример.

```csharp
using NativeShared;
...

CGRect rect = new CGRect (0, 0, 200, 200);
Console.WriteLine ("Rectangle Area: {0}", Transformations.CalculateArea ((RectangleF)rect));
```

Если получающее приложение выполняет сотни вызовы библиотек общего кода, это, может оказаться хорошим решением.

Зависимости от архитектуры наше приложение, мы может оказаться, используя один или несколько из приведенного выше решений для поддержки собственных данных типов (где необходимо) в нашем кросс платформенного кода.


## <a name="xamarinforms-applications"></a>Xamarin.Forms приложений

Для использования Xamarin.Forms для кросс платформенной пользовательских интерфейсов, которые также будут использоваться совместно с приложением единой API требуется следующее:

- Всего решения необходимо использовать до версии 1.3.1 (или более поздней) пакет NuGet Xamarin.Forms.
- Для любого Xamarin.iOS пользовательские модули подготовки используйте решения, представленные выше того же типа в зависимости от того, как был код пользовательского интерфейса общего (общий проект или PCL).

Как стандартного кросс платформенные приложения существующие 32-разрядные типы данных должны использоваться в любом коде общего, кросс платформенных в большинстве случаев все. Новые собственные типы данных использовать только при вызовах API-интерфейса Mac или iOS где поддержка поддержкой архитектуры типы являются обязательными.

Дополнительные сведения см. в разделе нашей [обновление существующих приложений Xamarin.Forms](http://developer.xamarin.com/guides/cross-platform/macios/updating-xamarin-forms-apps/) документации.

## <a name="summary"></a>Сводка

В этой статье у нас есть см. когда следует использовать собственные типы данных в приложение с единой API и их последствия между различными платформами. Мы предоставили несколько решений, которые можно использовать в ситуациях, где необходимо использовать новые собственные типы данных в межплатформенные библиотеки. И мы видели краткое руководство по поддержке единой API-интерфейсы в Xamarin.Forms кросс платформенных приложений.



## <a name="related-links"></a>Связанные ссылки

- [Unified API](~/cross-platform/macios/unified/index.md)
- [Собственные типы](~/cross-platform/macios/nativetypes.md)
- [Параметры совместного использования кода](~/cross-platform/app-fundamentals/code-sharing.md)
- [Пример совместного использования кода](https://developer.xamarin.com/samples/mobile/SharingCode/)
