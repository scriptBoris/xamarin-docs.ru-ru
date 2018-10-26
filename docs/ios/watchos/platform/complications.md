---
title: watchOS сложности в Xamarin
description: В этом документе описывается, как работать с watchOS сложности в Xamarin. Он описывает добавление усложнения, написание сложностью, шаблоны и примеры кода.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 85b0c9b0688e9fb310a8f427018a02fe629404bb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117750"
---
# <a name="watchos-complications-in-xamarin"></a>watchOS сложности в Xamarin

_watchOS позволяет разработчикам создавать пользовательские сложности для watch лиц_

На этой странице объясняется различные типы доступных сложностей и добавление усложнения приложение watchOS 3.

Обратите внимание на то, что каждое приложение watchOS может иметь только один усложнения.

Начните с изучения [документации Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) для определения, подходит ли приложение для сложностью. Существуют 5 `CLKComplicationFamily` типа отображения для выбора:

[![](complications-images/all-complications-sml.png "5 доступных типов CLKComplicationFamily: циклическая небольшой, модульная небольшой, модульная больших, привлекающий небольшой, привлекающий больших")](complications-images/all-complications.png#lightbox)

Приложения можно реализовать только один стиль или все пять, в зависимости от отображаемых данных.
Можно также поддерживают переход во времени, указав значения для прошлых и будущих значений времени, как пользователь перевернет цифровых корона.

<a name="adding" />

## <a name="adding-a-complication"></a>Добавление усложнения

### <a name="configuration"></a>Конфигурация

Сложности можно добавить приложение просмотра во время создания или вручную добавлены в существующее решение.

### <a name="add-new-project"></a>Добавьте новый проект...

**Добавить новый проект...**  мастер содержит флажок, который автоматически создает класс контроллера усложнения и настроить **Info.plist** файла:

![](complications-images/file-new-project-sml.png "Флажок включения усложнения")

### <a name="existing-projects"></a>Существующие проекты

Чтобы добавить существующий проект осложнение:

1. Создайте новый **ComplicationController.cs** файл класса и реализуйте `CLKComplicationDataSource`.
2. Настройка приложения **Info.plist** для предоставления усложнения и удостоверений, какие семейства усложнения поддерживаются.

Эти действия описаны более подробно ниже.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>Класс CLKComplicationDataSource

Следующие C# шаблон включает минимальные необходимые методы для реализации `CLKComplicationDataSource`.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
    }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
    }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
    }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
    }
}
```

Выполните [записи осложнение](#writing) инструкции по добавлению кода к этому классу.

### <a name="infoplist"></a>Info.plist

Расширение просмотра **Info.plist** файла следует указать имя `CLKComplicationDataSource` и какие семейства усложнения, вы хотите поддерживать:

[![](complications-images/complications-config-sml.png "Типы семейства усложнения")](complications-images/complications-config.png#lightbox)

**Класса источника данных** запись списке будут показаны имена классов этот подкласс `CLKComplicationDataSource` подкласс, включающий логику усложнения.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Все функциональные возможности усложнения реализуется в одном классе, переопределения методов из `CLKComplicationDataSource` абстрактного класса (который реализует `ICLKComplicationDataSource` интерфейс).

### <a name="required-methods"></a>Необходимые методы

Необходимо реализовать следующие методы для усложнения для запуска:

- `GetPlaceholderTemplate` — Возвращается статическим отображением, используется во время настройки, или если приложение не может предоставить значение.
- `GetCurrentTimelineEntry` -Рассчитать правильное отображение, при выполнении усложнения.
- `GetSupportedTimeTravelDirections` — Возвращает параметры из `CLKComplicationTimeTravelDirections` например `None`, `Forward`, `Backward`, или `Forward | Backward`.

### <a name="privacy"></a>Конфиденциальность

Сложности, которые отображают персональные данные

* `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` или `HideOnLockScreen`

Если этот метод возвращает `HideOnLockScreen` усложнения будет показано, либо значок или имя приложения (но не данные) при блокировке часами.

### <a name="updates"></a>Обновления

- `GetNextRequestedUpdateDate` -, Возвращают время при операционной системы Далее следует запрос в приложении для обновленных усложнения отображаемые данные.

Можно также принудительно обновить из приложения iOS.

### <a name="supporting-time-travel"></a>Поддержка Переход во времени

Время поездки поддержки является необязательным и по `GetSupportedTimeTravelDirections` метод. Если он возвращает `Forward`, `Backward`, или `Forward | Backward` затем должны реализовывать следующие методы

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Написание осложнение

Сложности в диапазоне от простых данных отображения сложных изображений и визуализации данных с поддержкой Переход во времени. В приведенном ниже коде показано, как создавать простые, один шаблон усложнения.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Пример кода

В этом примере поддерживает только `UtilitarianLarge` шаблона, поэтому можно выбрать только на определенные Контрольные значения лиц, которые поддерживают такого рода усложнения. При *выбора* сложности на watch, он отображает **Мои УСЛОЖНЕНИЯ** и когда *под управлением* отображается текст **МИНУТУ _час_**   (с частью времени).

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>Шаблоны усложнения

Существует несколько разных шаблонов для каждого стиля усложнения.
**Кольцо** шаблоны позволяют отображать ход выполнения style кольцо вокруг усложнения, который может использоваться для графического отображения хода выполнения или любое другое значение.

[Документация CLKComplicationTemplate Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Круглый малый

Эти имена классов шаблона начинаются с `CLKComplicationTemplateCircularSmall`:

- **RingImage** -отображения единый образ, кольца хода выполнения вокруг него.
- **RingText** -отображения одной строки текста с кольца хода выполнения вокруг него.
- **В образце SimpleImage** -просто отображают небольшой одного образа.
- **Программа Simple Text** -просто отображают небольшой фрагмент текста.
- **StackImage** -отображать изображения и строки текста, друг с другом
- **StackText** -вывести две строки текста.

### <a name="modular-small"></a>Модульный малый

Эти имена классов шаблона начинаются с `CLKComplicationTemplateModularSmall`:

- **ColumnsText** -отображать небольшой сетки значений текста (2 строки и 2 столбца).
- **RingImage** -отображения единый образ, кольца хода выполнения вокруг него.
- **RingText** -отображения одной строки текста с кольца хода выполнения вокруг него.
- **В образце SimpleImage** -просто отображают небольшой одного образа.
- **Программа Simple Text** -просто отображают небольшой фрагмент текста.
- **StackImage** -отображать изображения и строки текста, друг с другом
- **StackText** -вывести две строки текста.

### <a name="modular-large"></a>Модульный крупный

Эти имена классов шаблона начинаются с `CLKComplicationTemplateModularLarge`:

- **Столбцы** -отображения сетки 3 строки с двумя столбцами, при необходимости включая изображения слева от каждой строки.
- **StandardBody** -отображение полужирный заголовок строки, с двумя строками открытого текста. Заголовок при необходимости можно отобразить изображение в левой части.
- **Таблица** -отображение полужирный заголовок строки, с таблицей 2 x 2 текст под ним. Заголовок при необходимости можно отобразить изображение в левой части.
- **TallBody** -отображение полужирный заголовок строки, с размером шрифта одна строка текста под.

### <a name="utilitarian-small"></a>Практичное малый

Эти имена классов шаблона начинаются с `CLKComplicationTemplateUtilitarianSmall`:

- **Плоский** -отображает изображение и некоторый текст в одной строке (текст должен быть коротким).
- **RingImage** -отображения единый образ, кольца хода выполнения вокруг него.
- **RingText** -отображения одной строки текста с кольца хода выполнения вокруг него.
- **Квадрат** -square изображения (40px или 44px квадрат для 38 мм или 42 мм Apple Watch соответственно).

### <a name="utilitarian-large"></a>Практичное больших

Имеется только один шаблон для этого стиля усложнения: `CLKComplicationTemplateUtilitarianLargeFlat`.
Отображает одно изображение и некоторый текст, все в одной строке.



## <a name="related-links"></a>Связанные ссылки

- [Документация Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [WWDC видео](https://developer.apple.com/videos/play/wwdc2015-209/)
