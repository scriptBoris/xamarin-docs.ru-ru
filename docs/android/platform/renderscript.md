---
title: Общие сведения о Renderscript
description: В этом руководстве представлены Renderscript и содержит сведения об использовании встроенных Renderscript интерфейса API в приложениях Xamarin.Android этого предназначена для API уровня 17 или более поздней версии.
ms.prod: xamarin
ms.assetid: 378793C7-5E3E-40E6-ABEE-BEAEF64E6A47
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5369542552a41100443c5e91ceca9e110c5c7c3c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108734"
---
# <a name="an-introduction-to-renderscript"></a>Общие сведения о Renderscript

_В этом руководстве представлены Renderscript и содержит сведения об использовании встроенных Renderscript интерфейса API в приложениях Xamarin.Android этого предназначена для API уровня 17 или более поздней версии._

## <a name="overview"></a>Обзор

RenderScript — это платформа программирования, разработанную компанией Google, в целях повышения производительности приложений Android, которые потребоваться большое количество вычислительных ресурсов. Это низкого уровня, высокий уровень производительности на основе API [C99](http://en.wikipedia.org/wiki/C99). Так как это низкоуровневый API, который будет выполняться на ЦП, GPU и DSP Renderscript идеально подходит для приложений Android, может потребоваться выполнить одно из следующих:

* Графика
* Обработка изображений
* Шифрование
* Обработка сигнала
* Математические подпрограммы

Будет использовать RenderScript `clang` и скомпилировать скрипты для LLVM байтовый код, который объединяется в пакет APK. При первом запуске приложения, LLVM байт-кода будут скомпилированы в машинный код для процессоров на устройстве. Эта архитектура позволяет воспользоваться преимуществами машинный код без разработчики сами его записи для каждого процессора на устройстве самих приложений Android.

Есть два компонента в подпрограмму Renderscript:

1. **Среда выполнения Renderscript** &ndash; это собственные интерфейсы API, которые отвечают за выполнение Renderscript. Сюда входят любые Renderscripts, написанные для приложения.

2. **Управляемые оболочки из платформы Android** &ndash; управляемые классы, позволяющие приложение Android для управления и взаимодействия с Renderscript среды выполнения и скрипты. Помимо классов платформы для управления Renderscript среды выполнения цепочке инструментов Android изучите Renderscript исходный код и создавать управляемые классы-оболочки для использования приложением Android.

На следующей схеме показана, как связаны эти компоненты:

![Схема, иллюстрирующая взаимодействие с исполняющей средой Renderscript платформы Android](renderscript-images/renderscript-01.png)

Существует три важные понятия по использованию Renderscripts в приложении Android.

1. **Контекст** &ndash; управляемого интерфейса API, предоставленного пакета SDK для Android, который выделяет ресурсы Renderscript и позволяет передавать и принимать данные от Renderscript приложения Android.

2. **Объект _вычислительных ядра_**  &ndash; также называется _ядра корневой_ или _ядра_этот подпрограмму, которая выполняет работу. Ядро будет очень похожа на функцию C; Это параллельно подпрограмму, которая будет выполняться по всем данным в выделенной памяти.

3. **Выделено памяти** &ndash; данные передаются в и из ядра через  _[выделения](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)_. Ядро может иметь один вход и/или выходным выделения.

[Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/) пространство имен содержит классы для взаимодействия со средой выполнения Renderscript. В частности [ `Renderscript` ](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/) класс будет управлять жизненным циклом и ресурсы обработчика Renderscript. Приложение Android необходимо инициализировать один или несколько [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
Объекты. Распределение — это управляемый интерфейс API, который отвечает за выделение и доступ к памяти, который разделяется между приложения Android и Renderscript среды выполнения. Как правило для входных данных создается одно выделение и выделение при необходимости другой создается для хранения выходных данных ядра. Подсистема среды выполнения Renderscript и связанные управляемые классы-оболочки будет управлять доступом к памяти, занимаемый объем распределений, нет необходимости для разработчика приложений Android для выполнения дополнительных действий.

Выделение ресурсов будет содержать один или несколько [Android.Renderscripts.Elements](https://developer.xamarin.com/api/type/Android.Renderscripts.Element/).
Элементы представляют собой особый тип, который описывают данные в каждом выделении памяти.
Типы элементов выходных данных распределения должны совпадать с типами элемента input. При выполнении, Renderscript переберет каждого элемента в входной выделения в параллельном режиме и записи результатов в выходные данные выделения. Существует два типа элементов:

- **простой тип** &ndash; концептуально это то же самое, как тип данных C `float` или `char`.

- **Сложный тип** &ndash; этот тип аналогичен C `struct`.

Ядро Renderscript выполнит проверку времени выполнения, чтобы убедиться, что элементы в каждом выделении совместимы с того, что требуется ядро. Если тип данных элементов в выделении, соответствуют ли тип данных, который ожидает ядро, будет вызвано исключение.

Все ядра Renderscript будет заключена с типом, который является потомком [`Android.Renderscripts.Script`](https://developer.xamarin.com/api/type/Android.Renderscripts.Script/)
. `Script` Класс используется для задания параметров для Renderscript, задайте необходимые `Allocations`, и запустите Renderscript. Существует два `Script` подклассов в пакете SDK для Android:


- **`Android.Renderscripts.ScriptIntrinsic`** &ndash; Некоторые из более распространенных задач Renderscript объединенные в пакет SDK для Android и доступны по подкласс [ScriptIntrinsic](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic/) класса. Нет необходимости для разработчика любые дополнительные меры для использования этих скриптов в своих приложениях, так как они уже представлены.

- **`ScriptC_XXXXX`** &ndash; Также называется _пользовательские скрипты_, это скрипты, создаваемые разработчиками и упаковываются в пакет APK. Во время компиляции в цепочке инструментов Android будет создавать управляемые классы-оболочки, которые дадут сценарии для использования в приложении Android.
  Эти созданные классы называется имя файла Renderscript, начинаются с префикса `ScriptC_`. Написание и Включение сценариев пользователя официально не поддерживается для Xamarin.Android и выходит за рамки данного руководства.

Из этих двух типов, только `StringIntrinsic` поддерживается в Xamarin.Android. В этом руководстве описывается, как использовать встроенные сценарии в приложениях Xamarin.Android.

## <a name="requirements"></a>Требования

Это руководство предназначено для приложений Xamarin.Android, уровень целевой API 17 или более поздней версии. Использование _пользовательские скрипты_ в этом руководстве не рассматривается.

[Библиотека поддержки V8 Xamarin.Android](https://www.nuget.org/packages/Xamarin.Android.Support.v8.RenderScript/) бэкпортированным встроенных Renderscript API для приложений, предназначенных для более старых версиях пакета SDK для Android. Добавление этого пакета в проект Xamarin.Android должен позволять приложений, нацеленных на более старые версии пакета Android SDK. Чтобы использовать встроенные сценарии.

## <a name="using-intrinsic-renderscripts-in-xamarinandroid"></a>С помощью встроенных Renderscripts в Xamarin.Android

Встроенные сценарии являются отличным способом выполнения ресурсоемких вычислительных задач, с минимальным объемом дополнительного кода. Они были вручную настроены так, чтобы обеспечивают оптимальную производительность на большой раздел разных устройств.
Нередко встроенный скрипт для запуска 10 раз быстрее, чем управляемого кода и 2 – 3 раза больше после чем пользовательские реализации C. Множество типичных сценариев, охватываются встроенных скриптов. Этот список встроенных скриптов описаны текущие скрипты в Xamarin.Android.

- [ScriptIntrinsic3DLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsic3DLUT//) &ndash; преобразует RGB в RGBA, с помощью таблицы подстановки 3D. 

- [ScriptIntrinsicBLAS](https://developer.android.com/reference/android/renderscript/ScriptIntrinsicBLAS.html) &ndash; Provideshigh производительности Renderscript API-интерфейсы для [BLAS](http://www.netlib.org/blas/). BLAS (основные подпрограмм линейной алгебры) являются подпрограммами, которые предоставляются стандартных блоков для выполнения основных вектора и матричных операциях. 

- [ScriptIntrinsicBlend](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlend) &ndash; объединяет два выделения друг с другом.

- [ScriptIntrinsicBlur](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicBlur) &ndash; размытым гауссова распределения.

- [ScriptIntrinsicColorMatrix](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicColorMatrix/) &ndash; применяется матрицы цветов для выделения (т. е. Изменение цветов, измените оттенок).

- [ScriptIntrinsicConvolve3x3](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve3x3/) &ndash; применяет матрицу 3 x 3 цвет для распределения.

- [ScriptIntrinsicConvolve5x5](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicConvolve5x5/) &ndash; применяет матрицу 5 x 5 цвет для распределения.

- [ScriptIntrinsicHistogram](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicHistogram/) &ndash; фильтром внутренние гистограммы.

- [ScriptIntrinsicLUT](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicLUT/) &ndash; применяет таблицу уточняющих запросов для каждого канала в буфер.

- [ScriptIntrinsicResize](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicResize/) &ndash; скрипт для выполнения изменения размера 2D выделения.

- [ScriptIntrinsicYuvToRGB](https://developer.xamarin.com/api/type/Android.Renderscripts.ScriptIntrinsicYuvToRGB/) &ndash; Преобразует буфер YUV в RGB.

Дополнительные сведения о всех встроенных скриптов обратитесь к документации API.

Далее описаны основные действия по использованию Renderscript в приложении Android.

**Создайте контекст Renderscript** &ndash; [`Renderscript`](https://developer.xamarin.com/api/type/Android.Renderscripts.RenderScript/)
класс является управляемую оболочку Renderscript контексте и будет управлять инициализации, управление ресурсами и очистки. Объект Renderscript создается с помощью `RenderScript.Create` фабричный метод, который принимает контексте Android (например, действия) как параметр. Следующая строка кода показано, как инициализировать контекст Renderscript:

```csharp
Android.Renderscripts.RenderScript renderScript = RenderScript.Create(this);
```

**Создать распределения** &ndash; в зависимости от встроенный сценарий, может быть необходимо создать один или два `Allocation`s. В [`Android.Renderscripts.Allocation`](https://developer.xamarin.com/api/type/Android.Renderscripts.Allocation/)
класс имеет несколько методов фабрики для создания экземпляра выделение для встроенных. Например следующий фрагмент кода демонстрирует создание распределения для растровых изображений.

```csharp
Android.Graphics.Bitmap originalBitmap;
Android.Renderscripts.Allocation inputAllocation = Allocation.CreateFromBitmap(renderScript,
                                                     originalBitmap,
                                                     Allocation.MipmapControl.MipmapFull,
                                                     AllocationUsage.Script);
```

Часто будет необходимо для создания `Allocation` для хранения выходных данных сценария. Это в следующем фрагменте показано, как использовать `Allocation.CreateTyped` вспомогательный метод для создания экземпляра секунды `Allocation` что того же типа, что и исходный:

```csharp
Android.Renderscripts.Allocation outputAllocation = Allocation.CreateTyped(renderScript, inputAllocation.Type);
```

**Создать экземпляр сценария оболочки** &ndash; каждый из классов-оболочек встроенный сценарий должен иметь вспомогательные методы (обычно называется `Create`) для создания экземпляра объект-оболочку для данного сценария. В следующем фрагменте кода приведен пример того, как создать экземпляр `ScriptIntrinsicBlur` Размытие объекта. `Element.U8_4` Создает элемент, описывающий тип данных, который является 4 поля значений 8-разрядное целое число без знака, подходящий для хранения данных, вспомогательный метод `Bitmap` объекта:

```csharp
Android.Renderscripts.ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.Create(renderScript, Element.U8_4(renderScript));
```

**Назначить Allocation(s), задать параметры и запустить сценарий** &ndash; `Script` класс предоставляет `ForEach` метод для фактического выполнения Renderscript. Этот метод будет перебора всех `Element` в `Allocation` набор входных данных. В некоторых случаях может потребоваться для предоставления `Allocation` , содержащий выходные данные.
`ForEach` перезапишет содержимое выходных данных выделения. Выполнять с помощью фрагментов кода из предыдущих шагов, в этом примере показано, как назначить входной распределения, задается значение параметра и наконец запустить сценарий (копирование результатов в выходные данные выделения):

```csharp
blurScript.SetInput(inputAllocation);
blurScript.SetRadius(25);  // Set a pamaeter
blurScript.ForEach(outputAllocation);
```

Вы можете посетить [размытие изображения с Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript) рецепт, он приведен полный пример того, как использовать встроенный сценарий в Xamarin.Android.

## <a name="summary"></a>Сводка

Это руководство содержит общие Renderscript и способы ее использования в приложениях Xamarin.Android. Она кратко описано Renderscript является и как он работает в приложении Android. Некоторые из ключевых компонентов в Renderscript и разницу между описывались _пользовательские скрипты_ и _встроенных скриптов_. Наконец в этом руководстве рассматриваются действия, описанные в с помощью встроенных сценария в приложениях Xamarin.Android.



## <a name="related-links"></a>Связанные ссылки

- [Пространство имен Android.Renderscripts](https://developer.xamarin.com/api/namespace/Android.Renderscripts/)
- [Размытие изображения с Renderscript](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/drawing/blur_an_image_with_renderscript)
- [Renderscript](https://developer.android.com/guide/topics/renderscript/compute.html)
- [Учебник: Приступая к работе с Renderscript](https://software.intel.com/en-us/articles/renderscript-basic-sample-for-android-os)
