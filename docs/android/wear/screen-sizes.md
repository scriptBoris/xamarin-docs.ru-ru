---
title: Работа с размерами экрана в Xamarin.Android и износа ОС
ms.prod: xamarin
ms.assetid: 77831169-C663-4D42-B742-B8B556B1DA4B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/25/2018
ms.openlocfilehash: 9fc22a3c08b60a8474b006f1c9225155b9705507
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113122"
---
# <a name="working-with-screen-sizes"></a>Работа с размерами экрана

Устройства Android Wear может иметь прямоугольный или round отображения, который также может быть разного размера.

![Отображает снимки экрана прямоугольных и round Wear](screen-sizes-images/moyeu-wear.png)

## <a name="identifying-screen-type"></a>Определение типа экрана

Библиотека поддержки износа предоставляет некоторые элементы управления, которые помогают обнаруживать и адаптироваться к другой экран фигур, таких как `WatchViewStub` и `BoxInsetLayout`.

Имейте в виду, что некоторые из других поддерживают элементы управления библиотеки (например, `GridViewPager`) *автоматически* обнаруживать фигуры на экране, сами и не должны добавляться как дочерние элементы управления описано ниже.

### <a name="watchviewstub"></a>WatchViewStub

См. в разделе [WatchViewStub](https://developer.xamarin.com/samples/WatchViewStub/) чтобы узнать, как определить тип экрана и отобразить структуру для каждого типа.

Файл основной макет содержит `android.support.wearable.view.WatchViewStub` которая ссылается на различные разметки для прямоугольных и round экранах, используя `app:rectLayout` и `app:roundLayout` атрибуты:

```xml
<android.support.wearable.view.WatchViewStub
    xmlns:app="http://schemas.android.com/apk/res-auto"
  android:layout_width="match_parent"
  android:layout_height="match_parent"
  android:id="@+id/stub"
  app:rectLayout="@layout/rect_layout"
  app:roundLayout="@layout/round_layout" />
```

Решение содержит различные разметки для каждого стиля, который будет выбрана во время выполнения.

![Файлы, показанные в группе ресурсов и расположение](screen-sizes-images/solution.png)


### <a name="boxinsetlayout"></a>BoxInsetLayout

А не создавать разные макеты для каждого типа экрана, можно также создать одно представление, адаптируется к экранам прямоугольный или round.

Это [пример Google](https://developer.android.com/training/wearables/ui/layouts.html#same-layout) показано, как использовать `BoxInsetLayout` использовать тот же макет экранов прямоугольная и округления.


## <a name="wear-ui-designer"></a>Wear конструктор пользовательского интерфейса

Конструктор Android Xamarin поддерживает прямоугольных и round экранов:

![Выбор экрана Android Wear квадрат в конструкторе Android Xamarin](screen-sizes-images/design-screen-type.png)

В области конструктора в прямоугольную стиля показан ниже:

![Область конструктора в прямоугольную стиля](screen-sizes-images/design-rect.png) 

В области конструктора в округления показан ниже:

![Область конструктора в округления](screen-sizes-images/design-round.png)


## <a name="wear-simulator"></a>Симулятор wear

**Диспетчер эмуляторов Google** содержит определения устройств, для обоих типы экранов. Вы можете создать прямоугольных и round эмуляторов для тестирования приложения.

![Wear определения устройств, показан диспетчер эмуляторов Google](screen-sizes-images/emulator-devices.png)

Эмулятор будет отображаться следующим образом для прямоугольном экране:

![Эмулятор отрисовка прямоугольном экране](screen-sizes-images/recipe-2.png) 

Он будет отображаться следующим образом для круглый экран:

![Эмулятор отрисовка круглый экран](screen-sizes-images/recipe-2-round.png)

## <a name="video"></a>Видео

[Во весь экран приложения для Android Wear](https://www.youtube.com/watch?v=naf_WbtFAlY) из [developers.google.com](https://www.youtube.com/channel/UC_x5XG1OV2P6uZZ5FSM9Ttw).

