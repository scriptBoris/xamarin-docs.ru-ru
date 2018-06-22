---
title: Общие сведения о Renderscript
description: В этом руководстве представлены Renderscript и объясняется, как использовать встроенный Renderscript API-Интерфейсов в приложении Xamarin.Android этот уровень предназначена для API, 17 и выше.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: f9e21a51c409c5444f137a63eb2c6fadfef03cbe
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772020"
---
# <a name="an-introduction-to-renderscript"></a>Общие сведения о Renderscript

_В этом руководстве представлены Renderscript и объясняется, как использовать встроенный Renderscript API-Интерфейсов в приложении Xamarin.Android этот уровень предназначена для API, 17 и выше._

## <a name="overview"></a>Обзор

RenderScript представляют собой платформу программирования, созданные Google с целью повышения производительности приложений Android, которым требуется большое количество вычислительных ресурсов. Это низкого уровня, высокий уровень производительности на основе API [C99](http://en.wikipedia.org/wiki/C99). Так как это низкий уровень API, который будет выполняться на ЦП, графических процессоров или DSP Renderscript идеально подходит для приложений Android, может потребоваться выполнить одно из следующих:

* Графика
* Обработка изображений
* Шифрование
* Обработка сигналов
* Математические подпрограммы

Будет использовать RenderScript `clang` и скомпилировать скрипты для LLVM байтовый код, который объединяется в APK. При первом запуске приложения, LLVM байтовый код компилируется в машинный код для процессоров на устройстве. Эта архитектура позволяет приложению Android воспользоваться преимуществами машинный код без разработчики сами его записи для каждого процессора на устройстве сами.

Есть два компонента в подпрограмму Renderscript:

1. **Среда выполнения Renderscript** &ndash; это собственный API, которые отвечают за выполнение Renderscript. Сюда входят любые Renderscripts, написанные для приложения.

2. **Управляемые оболочки из платформы Android** &ndash; управляемые классы, позволяющие приложение Android для управления и взаимодействия с Renderscript среды выполнения и сценариев. Наряду с классами платформы для контроля выполнения Renderscript Android инструментов изучите исходный код Renderscript и создавать управляемые классы-оболочки для использования в приложении Android.

Следующая диаграмма иллюстрирует, как связаны эти компоненты:

![Диаграмма, иллюстрирующая взаимодействие со средой выполнения Renderscript платформы Android](renderscript-images/renderscript-01.png)

Существует три важных понятий, относящихся к использованию Renderscripts в приложение.

1. **Контекст** &ndash; управляемого API, предоставляемые пакета SDK для Android, которая выделяет ресурсы Renderscript и позволяет передавать и принимать данные от Renderscript приложения Android.

2. **Объект _вычислений ядра_**  &ndash; также называется _ядра корневой_ или _ядра_, то подпрограмму, которая выполняет работу. Ядро очень похожа на функцию C; Это параллельно подпрограмму, которая будет выполняться в отношении всех данных в памяти.

3. **Выделяет память** &ndash; данные передаются в и из ядра через  _[выделения](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Ядро может иметь один вход и один выходной распределения.

[Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) пространство имен содержит классы для взаимодействия со средой выполнения Renderscript. В частности [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) класса будут управлять жизненным циклом и ресурсы Renderscript ядра. Приложение Android необходимо инициализировать один или несколько [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/) объектов. Выделение является управляемый интерфейс API, который отвечает за выделение и доступ к памяти, который совместно используется приложение Android и Renderscript среды выполнения. Как правило один распределение создается для входных данных и при необходимости другой распределение создается для хранения выходных данных ядра. Подсистема выполнения Renderscript и связанные управляемые классы-оболочки будут управлять доступом к памяти, занимаемый объем выделенной памяти, нет необходимости разработчик приложения Android для выполнения дополнительных действий.

Выделение будет содержать один или несколько [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Элементы представляют собой особый тип, описывающих данные в каждом выделении памяти.
Типы элементов этих выходных данных должно соответствовать выделения типа входного элемента. При выполнении Renderscript переберет каждого элемента входной выделения в параллельном режиме и записи результатов в выходные данные выделения. Существует два типа элементов:

- **простой тип** &ndash; по сути это то же самое, как тип данных C `float` или `char`.

- **Сложный тип** &ndash; этот тип аналогичен C `struct`.

Ядро Renderscript выполнит проверку среды выполнения, чтобы обеспечить совместимость с ресурсов, необходимых в ядре элементов в каждом выделении памяти. Если тип данных элементов при распределении тип данных, который ожидается ядра не совпадают, будет вызвано исключение.

Все ядра Renderscript будет упаковано с помощью типа, который является потомком [ `Android.Renderscripts.Script` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/) класса. `Script` Класс используется для задания параметров для Renderscript, настройте соответствующие `Allocations`, и запустите Renderscript. Существует два `Script` подклассов в Android SDK:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Некоторые из наиболее распространенных задач Renderscript объединенными в пакет SDK Android и доступны по подкласс [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) класса. Разработчик пройти все дополнительные шаги, чтобы использовать эти сценарии в своих приложениях, как они уже представлены нет необходимости.

- **`ScriptC_XXXXX`** &ndash; Также называется _сценариев пользователя_, сценарии, написанные разработчиками и в APK. Во время компиляции Android инструментов будет создавать управляемые классы-оболочки, позволяющих сценарии для использования в приложении Android.
  Эти созданные классы называется имя файла Renderscript с префиксом `ScriptC_`. Создание и внедрение сценариев пользователя официально не поддерживается, Xamarin.Android и выходит за рамки данного руководства.

В этих двух типах только `StringIntrinsic` поддерживается Xamarin.Android. В этом руководстве рассматривается использование встроенных скриптов в приложении Xamarin.Android.

## <a name="requirements"></a>Требования

Это руководство предназначено для приложений Xamarin.Android этого целевой API уровня 17 и выше. Использование _пользовательские скрипты_ в этом руководстве не рассматривается.

[Библиотека поддержки V8 Xamarin.Android](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) backports встроенных Renderscript API для приложений, предназначенных для более старых версий пакета SDK для Android. Добавление этого пакета в проект Xamarin.Android должен позволять приложений, нацеленных более старые версии пакета SDK для Android для использования встроенных скриптов.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>Использование встроенных Renderscripts в Xamarin.Android

Встроенная функция сценариев — отличный способ выполнения ресурсоемких вычислительных задач с минимальным объемом дополнительный код. Они были настроены для обеспечения оптимальной производительности на большой раздел между устройств вручную.
Довольно часто для выполнения 10 раз быстрее, чем управляемый код и 2 – 3 раза больше после чем пользовательская реализация C встроенная функция сценария. Множество типичных сценариев покрываются встроенных скриптов. Это встроенная функция скриптов перечислены текущие сценарии в Xamarin.Android:

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; преобразует RGB в RGBA, с помощью таблицы подстановки 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh производительности Renderscript API-интерфейсы для [BLAS](http://www.netlib.org/blas/). BLAS (Basic подпрограмм линейной алгебры) являются процедуры, которые предоставляются стандартных блоков для выполнения операции матрицы и основные вектор. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; объединяет два выделения друг с другом.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; применяется гауссово размытие выделения.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; применяется матрицы цветов для выделения ресурсов (т. е. Изменение цветов, измените оттенка).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; применяет матрицу 3 x 3 цвет для распределения.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; применяется к распределению матрицы цветов 5 x 5.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; фильтр встроенная функция гистограммы.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; применяет таблицу уточняющих запросов для каждого канала в буфер.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; сценария для выполнения изменения размера 2D выделения.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; Преобразует буфер YUV в RGB.

Обратитесь к документации по API сведения о каждом из встроенных скриптов.

Далее описаны основные шаги по использованию Renderscript в приложение.

**Создать контекст Renderscript** &ndash; [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) класс является управляемую оболочку Renderscript контекста и управлять инициализация, управление ресурсами и очистки. Объект Renderscript создается с помощью `RenderScript.Create` фабричный метод, принимающий контекст Android (например, действия) как параметр. Следующая строка кода показано, как инициализировать контекст Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Создать распределения** &ndash; в зависимости от встроенный скрипт может оказаться необходимым создать один или два `Allocation`s. [ `Android.Renderscripts.Allocation` ](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/) Класс содержит несколько методов фабрики для создания экземпляра выделение для встроенной функции. Например следующий фрагмент кода демонстрирует создание распределения для растровых изображений.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Часто, его будет необходимо создать `Allocation` для хранения выходных данных скрипта. Это следующий фрагмент кода показывает использование `Allocation.CreateTyped` вспомогательный метод для создания второго `Allocation` , того же типа, что и исходный:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Создать экземпляр сценария оболочки** &ndash; каждый из классов-оболочек встроенный скрипт должен иметь вспомогательные методы (обычно называется `Create`) для создания экземпляра объект-оболочка для этого скрипта. В следующем фрагменте кода приведен пример создания экземпляров `ScriptIntrinsicBlur` Размытие объекта. `Element.U8_4` Вспомогательный метод создаст элемент, описывающий тип данных, который является 4 поля значений, 8-разрядное целое число без знака, подходящих для хранения данных `Bitmap` объекта:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Назначьте Allocation(s), задать параметры и запустить сценарий** &ndash; `Script` класс предоставляет `ForEach` метод для фактического выполнения Renderscript. Этот метод будет перебора всех `Element` в `Allocation` удерживая входных данных. В некоторых случаях может быть необходимо для предоставления `Allocation` , содержащий выходные данные.
`ForEach` перезапишет содержимое выходных данных распределения. Выполнять с помощью фрагментов кода на предыдущих шагах, в этом примере показано, как назначение входной выделение, задается значение параметра и наконец запустить сценарий (копирование результатов в выходные данные выделения):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Вы можете извлечь [размывает изображение с Renderscript](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/) инструкций, он приведен полный пример того, как использовать встроенный сценарий в Xamarin.Android.

## <a name="summary"></a>Сводка

В этом руководстве представлена Renderscript и способ его использования в приложении Xamarin.Android. Он кратко рассматриваются Renderscript — и как она работает в приложение. Оно описано некоторые ключевые компоненты в Renderscript и разницу между _пользовательские скрипты_ и _встроенных скриптов_. Наконец в этом руководстве описаны действия, описанные в с помощью встроенных сценария в приложении Xamarin.Android.



## <a name="related-links"></a>Связанные ссылки

- [Android.Renderscripts namespace](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [Размывает изображение с Renderscript](https://developer.xamarin.com/recipes/android/other_ux/drawing/blur_an_image_with_renderscript/)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Учебник: Приступая к работе с Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
