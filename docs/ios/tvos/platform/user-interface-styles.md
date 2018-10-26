---
title: tvOS стили пользовательского интерфейса в Xamarin
description: В этой статье рассматриваются света и Темная темы пользовательского интерфейса, Apple добавлено tvOS 10, а также способы их реализации в приложении Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: 8BC37683-AD9E-45CD-BE40-96965618AD1D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 2536ca5d3bff3f5b7962bc4fcf58b31a130fd03c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50104769"
---
# <a name="tvos-user-interface-styles-in-xamarin"></a>tvOS стили пользовательского интерфейса в Xamarin

_В этой статье рассматриваются света и Темная темы пользовательского интерфейса, Apple добавлено tvOS 10, а также способы их реализации в приложении Xamarin.tvOS._

tvOS 10 теперь поддерживает свет пользовательский интерфейс и Темная темы, что все сборки в UIKit элементы управления будут автоматически адаптироваться к, зависит от предпочтений пользователя. Кроме того можно вручную настроить элементы пользовательского интерфейса на основе темы, которую пользователь выбрал разработчик и можно переопределить данной темы.

<a name="About-the-New-User-Interface-Styles" />

## <a name="about-the-new-user-interface-styles"></a>О новые стили пользовательского интерфейса

Как уже говорилось, tvOS 10 теперь поддерживает свет пользовательский интерфейс и Темная темы, что все сборки в UIKit элементы управления будут автоматически адаптироваться к, зависит от предпочтений пользователя.

Пользователь может переключать эту тему, выбрав **параметры** > **Общие** > **внешний вид** и переключение между **света**  и **темно-**:

[![](user-interface-styles-images/theme01.png "Параметры приложения")](user-interface-styles-images/theme01.png#lightbox)

Когда **темно-** темы установлен, все элементы пользовательского интерфейса будет переключен в светлой текст на темном фоне:

[![](user-interface-styles-images/theme02.png "\"Темной\" теме")](user-interface-styles-images/theme02.png#lightbox)

Пользователь имеет возможность смены темы в любое время и может сделать это на текущее действие, где находится Apple TV или время суток, на основе.

Свет пользовательского интерфейса используется по умолчанию тема, и всех имеющихся приложений tvOS по-прежнему использовать Светлая тема, независимо от того, персональные настройки пользователя, если только они будут изменены для tvOS 10, чтобы воспользоваться преимуществами темной темы. Приложение tvOS 10 также имеет возможность переопределить текущей темы и всегда использовать Light или Темная тема для некоторых или всех пользовательского интерфейса.

<a name="Adopting-the-Light-and-Dark-Themes" />

## <a name="adopting-the-light-and-dark-themes"></a>Внедрение светлую и темную темы

Для поддержки этой возможности, Apple добавлен новый API для `UITraitCollection` класс и приложение tvOS должен согласиться для поддержки темно-внешний (через параметр в его `Info.plist` файла).

Чтобы согласиться на светлой и темной темы оформления, сделайте следующее:

1. В **обозревателе решений** дважды щелкните файл `Info.plist`, чтобы открыть его для редактирования.
2. Выберите **источника** представление (из нижней части редактора).
3. Добавьте новый ключ и назовите его `UIUserInterfaceStyle`: 

    [![](user-interface-styles-images/theme03.png "Ключ UIUserInterfaceStyle")](user-interface-styles-images/theme03.png#lightbox)
4. Оставьте для типа, значение `String` и введите значение `Automatic`: 

    [![](user-interface-styles-images/theme04.png "Введите автоматически")](user-interface-styles-images/theme04.png#lightbox)
5. Сохраните изменения в файле.

Существует три возможных значения для `UIUserInterfaceStyle` ключ:

- **Свет** -заставляет tvOS пользовательском Интерфейсе приложения всегда использовать Светлая тема.
- **Темно-** -заставляет tvOS пользовательском Интерфейсе приложения всегда использовать темной темы.
- **Автоматическое** -переключается между светлой и темной темы в зависимости от предпочтений пользователя в параметрах. Это предпочтительнее.

<a name="UIKit-Theme-Support" />

### <a name="uikit-theme-support"></a>Поддержка тем UIKit

Если приложение tvOS использует стандартные, встроенные `UIView` на основе элементов управления, они будут автоматически реагирующие на тему пользовательского интерфейса без какого-либо вмешательства разработчика.

Кроме того `UILabel` и `UITextView` автоматически произойдет Замена значения их цветов в зависимости от выберите тему пользовательского интерфейса:

- Текст будет черным "светлой" темой.
- Текст будет белый "темной" темой.

Если разработчик никогда не изменяет цвет текста вручную (с помощью раскадровки или код), они будут отвечает за обработку изменения цвета на основе темы пользовательского интерфейса.

<a name="New-Blur-Effects" />

### <a name="new-blur-effects"></a>Новые эффекты размытия

Для поддержки светлой и темной темах в приложение tvOS 10, Apple добавлены две новые эффекты размытия. Эти новые эффекты будут автоматически скорректированы размытия, на основе темы пользовательского интерфейса, который пользователь выбрал следующим образом:

- `UIBlurEffectStyleRegular` -Использует размытия источника света в "светлой" темой и темный размытия в "темной" теме.
- `UIBlurEffectStyleProminent` — Позволяет использовать очень света размытия "светлой" темой и очень темно-размытия, в "темной" теме.

<a name="Working-with-Trait-Collections" />

## <a name="working-with-trait-collections"></a>Работа с коллекциями признака

Новый `UserInterfaceStyle` свойство `UITraitCollection` класс может использоваться для получения текущего выбранного тему пользовательского интерфейса и будет `UIUserInterfaceStyle` enum одного из следующих значений:

- **Свет** -выбрана тема пользовательского интерфейса свет.
- **Темно-** -выбран пользовательского интерфейса темной темы.
- **Неизвестная** -вид не отобразился на экране, чтобы текущую тему пользовательского интерфейса неизвестно.

Кроме того коллекции признака имеют следующие возможности в tvOS 10:
 
- Можно настроить внешний вид прокси-сервер на основе `UserInterfaceStyle` из заданного `UITraitCollection` изменять такие параметры, такие как изображения или элемента на основе темы цвета.
- Приложение tvOS может обрабатывать изменения коллекции признака путем переопределения `TraitCollectionDidChange` метод `UIView` или `UIViewController` класса.

> [!IMPORTANT]
> Предварительная Xamarin.tvOS для tvOS 10 не поддерживает полностью `UIUserInterfaceStyle` для `UITraitCollection` еще. Полная поддержка будет добавлена в будущем выпуске.




<a name="Customizing-Appearance-Based-on-Theme" />

### <a name="customizing-appearance-based-on-theme"></a>Настройка внешнего вида, на основе темы

Для элементов пользовательского интерфейса, которые поддерживают внешний прокси-сервера их внешний вид можно изменить в зависимости от тему пользовательского интерфейса из их коллекции признака. Таким образом для данного элемента пользовательского интерфейса, разработчик может указать один цвет для светлой темы, а другой — для темной темы.

```csharp
button.SetTitleColor (UIColor.Red, UIControlState.Normal);

// TODO - Pseudocode because this isn't currently supported in the preview bindings.
var light = new UITraitCollection(UIUserInterfaceStyle.Light);
var dark = new UITraitCollection(UIUserInterfaceStyle.Dark);

button.ForTraitCollection(light).SetTitleColor (UIColor.Red, UIControlState.Normal);
button.ForTraitCollection(dark).SetTitleColor (UIColor.White, UIControlState.Normal);
```

> [!IMPORTANT]
> К сожалению, не поддерживает предварительный просмотр Xamarin.tvOS для tvOS 10 полностью `UIUserInterfaceStyle` для `UITraitCollection`, поэтому такая настройка еще не доступен. Полная поддержка будет добавлена в будущем выпуске.

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="responding-to-theme-changes-directly"></a>Реагирование на изменения темы, напрямую

В developer требует более глубокого контроля над внешним видом элемента пользовательского интерфейса на основе пользовательского интерфейса темы выбран, их можно переопределить `TraitCollectionDidChange` метод `UIView` или `UIViewController` класса.

Пример:

```csharp
public override void TraitCollectionDidChange (UITraitCollection previousTraitCollection)
{
    base.TraitCollectionDidChange (previousTraitCollection);
    
    // Take action based on the Light or Dark theme
    ...
}
```

<a name="Responding-to-Theme-Changes-Directly" />

### <a name="overriding-a-trait-collection"></a>Переопределение коллекция признака

Основываясь на дизайн приложения tvOS, могут существовать раз, когда разработчику необходимо переопределить Признак коллекции для указанного элемента пользовательского интерфейса, который будет всегда использовать конкретной темы пользовательского интерфейса.

Это можно сделать с помощью `SetOverrideTraitCollection` метод `UIViewController` класса. Пример:

```csharp
// Create new trait and configure it
var trait = new UITraitCollection ();
...

// Apply new trait collection
SetOverrideTraitCollection (trait, this);
```

Дополнительные сведения см. в разделе [Traits](~/ios/user-interface/storyboards/unified-storyboards.md) и [переопределение признаки](~/ios/user-interface/storyboards/unified-storyboards.md) части нашей [Знакомство с унифицированным раскадровкам](~/ios/user-interface/storyboards/unified-storyboards.md) документации.

<a name="Trait-Collections-and-Storyboards" />

### <a name="trait-collections-and-storyboards"></a>Признак коллекций и раскадровки

В tvOS 10 раскадровки приложения может быть присвоено реагировать на них признака коллекций и многие элементы пользовательского интерфейса можно узнавать светлой и темной темы. Текущей Xamarin.tvOS ранней предварительной версии для tvOS 10 не поддерживает эту функцию в конструкторе интерфейса, поэтому раскадровки потребуется изменить в конструктора Interface Builder в качестве обходного решения.

Чтобы включить поддержку признака коллекции, сделайте следующее:

1. Щелкните правой кнопкой мыши файл раскадровки в **обозревателе решений** и выберите **открыть с помощью** > **конструктора Interface Builder Xcode**: 

    [![](user-interface-styles-images/theme05.png "Открыть в построителе интерфейса Xcode")](user-interface-styles-images/theme05.png#lightbox) 
2. Чтобы включить поддержку признака, перейдите в **инспектор файлов** и проверьте **варианты использования признаков** свойство в **документ конструктора интерфейса** раздел: 

    [![](user-interface-styles-images/theme06.png "Включить поддержку признака коллекции")](user-interface-styles-images/theme06.png#lightbox)
3. Подтвердите изменение для использования варианты признаков: 

    [![](user-interface-styles-images/theme07.png "Использование оповещения варианты признаков")](user-interface-styles-images/theme07.png#lightbox)
4. Сохраните изменения в файл раскадровки.

При редактировании раскадровок tvOS в конструктор Interface Builder, Apple добавил следующие возможности:

* Разработчик может указать другие виды элементы пользовательского интерфейса на основе темы пользовательского интерфейса в **инспекторе атрибутов**:
    
    * Теперь имеют несколько свойств **+** рядом с ними, которые можно щелкнуть для добавления определенную версию пользовательского интерфейса темы: 

        [![](user-interface-styles-images/theme08.png "Добавление определенной версии тему пользовательского интерфейса")](user-interface-styles-images/theme08.png#lightbox) 
    
    * Разработчик может указать новое свойство или нажмите кнопку **x** кнопку, чтобы удалить его: 

        [![](user-interface-styles-images/theme09.png "Укажите новое свойство, или нажмите кнопку x, чтобы удалить его")](user-interface-styles-images/theme09.png#lightbox)
* Разработчик может просмотреть Дизайн пользовательского интерфейса в свет или Темная тема из внутри конструктора Interface Builder:
    
    * Внизу области конструктора позволяет разработчику переключаться текущую тему пользовательского интерфейса: 

        [![](user-interface-styles-images/theme10.png "Внизу рабочей области конструирования")](user-interface-styles-images/theme10.png#lightbox)
        
    * Новую тему будет отображаться в конструкторе Interface Builder и отображаются все коллекции признака определенные настройки: 

        [![](user-interface-styles-images/theme11.png "Темы, отображаемой в конструкторе Interface Builder")](user-interface-styles-images/theme11.png#lightbox)

Кроме того tvOS симулятор теперь имеет сочетание клавиш, чтобы позволить разработчику быстро переключаться между светлой и темной темах, при отладке приложения tvOS. Используйте **команда + Shift + D** сочетания последовательности можно переключаться между светлой и темной.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается света и Темная темы пользовательского интерфейса, Apple добавлено tvOS 10, а также способы их реализации в приложении Xamarin.tvOS.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Новые возможности в tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
