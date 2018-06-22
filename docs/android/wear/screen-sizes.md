---
title: Работа с размерами экранов в Xamarin.Android и износ ОС
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/25/2018
ms.openlocfilehash: 40e7850ffe239b0ede43e4d0cd3c6da08bce3a40
ms.sourcegitcommit: 4b0582a0f06598f3ff8ad5b817946459fed3c42a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "32436080"
---
# <a name="working-with-screen-sizes"></a>Работа с размерами экранов

Устройства Android одежды может иметь прямоугольный или round дисплея может также быть разного размера.

![Отображает снимки экрана износ прямоугольных и round](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Определение типа экрана

Библиотека поддержки износ предоставляет некоторые элементы управления, которые помогают обнаруживать и адаптироваться к другой экран фигур, таких как `WatchViewStub` и `BoxInsetLayout`.

Имейте в виду, что некоторые из других поддерживают элементы управления библиотеки (например, `GridViewPager`) *автоматически* самостоятельно обнаруживать экрана фигуры и не следует добавлять дочерних элементов управления, описанных ниже.

### <a name="watchviewstub"></a>WatchViewStub

В разделе [WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/) лучше понять, как определить тип экрана и отобразить структуру для каждого типа.

Содержит файл основного макета `android.support.wearable.view.WatchViewStub` разные макеты для прямоугольных и round экраны с помощью ссылки, которые `app:rectLayout` и `app:roundLayout` атрибуты:

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

Решение содержит разные макеты для каждого стиля, который будет выбрана во время выполнения:

![Файлы, показанный в разделе ресурсов и макет](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

Вместо того, чтобы создать разные макеты для каждого типа экрана, можно также создать единое представление, который используется для экранов прямоугольный или round.

Это [примере Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) показано, как использовать `BoxInsetLayout` использовать тот же макет экранов прямоугольная и округлить.


## <a name="wear-ui-designer"></a>Носят конструктор пользовательского интерфейса

Конструктор Xamarin Android поддерживает прямоугольных и round экраны:

![При выборе на экране квадрат износ Android в конструкторе Xamarin Android](screen-sizes-images/design-screen-type.png)

Области конструктора в прямоугольной стилей показан ниже:

![Область конструктора в прямоугольной стиля](screen-sizes-images/design-rect.png) 

Области конструктора в стиле round показан ниже:

![Область конструктора в стиле round](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Носят симулятора

**Диспетчера эмуляторов Google** содержит определения устройств, для обоих типов на экране. Можно создать прямоугольных и round эмуляторов, чтобы протестировать приложение.

![Носят определения устройств, показано в диспетчере эмуляторов Google](screen-sizes-images/emulator-devices.png)

Эмулятор будет отображаться следующим образом для прямоугольного экрана:

![Эмулятор визуализация прямоугольного экрана](screen-sizes-images/recipe-2.png) 

Round экрана будет заполняться следующим образом:

![Эмулятор отрисовки, round экрана](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Видео

[Во весь экран приложения для Android одежды](https://www.youtube.com/watch?v=naf_WbtFAlY) из [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

