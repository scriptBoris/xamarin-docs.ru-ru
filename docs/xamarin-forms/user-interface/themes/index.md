---
title: Темы Xamarin.Forms
description: В этой статье рассматриваются темы Xamarin.Forms, определяющим конкретных визуальное представление для стандартных представлений.
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0f49eeba072d6aeb7ead40d5d56d4af9e9bf5e27
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38814711"
---
# <a name="xamarinforms-themes"></a>Темы Xamarin.Forms

![](~/media/shared/preview.png "Этот API доступна в предварительной версии")

Темы Xamarin.Forms были объявлены в 2016 развития и доступны в предварительной версии для клиентов, оценить их и отправить отзыв.

Тема добавляется в приложение Xamarin.Forms, включив **Xamarin.Forms.Theme.Base** пакетов Nuget, а также дополнительный пакет, который определяет конкретной темы (например) Xamarin.Forms.Theme.Light) либо в противном случае можно задать локальный темы для приложения.

Ссылаться на ["светлой" теме](light.md) и ["темной" теме](dark.md) страниц инструкции о том, как добавить их в приложение, или ознакомьтесь с возможностями [пример пользовательской темы](custom.md).

**Важно:** необходимо также выполнить шаги, чтобы [загружать темы сборки (см. ниже)](#loadtheme) , добавив некоторые стандартный код для iOS `AppDelegate` и Android `MainActivity`. Это будет улучшена в будущих предварительной версии.


## <a name="control-appearance"></a>Внешний вид элемента управления

[Свет](light.md) и [темно-](dark.md) темы оба определяют определенный внешний вид для стандартных элементов управления. Когда вы добавите темы словарь ресурсов приложения, изменится внешний вид стандартным элементам управления.

В следующей разметке XAML показан некоторые общие элементы управления:

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

Эти снимки экрана Показать эти элементы управления с помощью:

* Тема не применяется
* Светлая тема (только небольшие отличия, которые необходимости без темы)
* Темная тема

![](images/standard-none-sml.png "Элементы управления без использования тем") ![](images/standard-light-sml.png "элементы управления со \"светлой\" теме") ![](images/standard-dark-sml.png "элементов управления с \"темной\" теме")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

`StyleClass` Свойство позволяет внешний вид представления, необходимо изменить в соответствии с определением, предоставляемые темы.

[Свет](light.md) и [темно-](dark.md) темы оба определяют три другого внешнего вида `BoxView`: `HorizontalRule`, `Circle`, и `Rounded`. Эта разметка показаны три различных `BoxView`s с классами другой стиль применяется:

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Это делает с светлый и темный следующим образом:

![](images/boxview-light-sml.png "BoxView с StyleClass Светлая тема") ![](images/boxview-dark-sml.png "BoxView с StyleClass \"темной\" теме")

<a name="builtin" />

## <a name="built-in-classes"></a>Встроенные классы

В дополнение к автоматически Стилизация общего элемента управления источника света и Темная темы в настоящее время поддерживает следующие классы, которые могут быть применены, задав `StyleClass` для этих элементов управления:

**BoxView**

* HorizontalRule
* Круг
* Округлено

**Изображение**

* Круг
* Округлено
* Эскиз

**Button**

* Значение по умолчанию
* Первичный
* Success
* Info
* Предупреждение
* Опасность
* Ссылка
* Небольшой
* Большой

**Label**

* Header
* Подзаголовок
* Текст
* Ссылка
* Обратное значение


## <a name="troubleshooting"></a>Устранение неполадок

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Не удалось загрузить файл или сборку «Xamarin.Forms.Theme.Light» или одну из ее зависимостей

В предварительной версии темы могут находиться не удалось загрузить во время выполнения. Добавьте код, показанный ниже, в соответствующие проекты для устранения этой ошибки.

**iOS**

В **AppDelegate.cs** добавьте следующие строки после `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.iOS.UnderlineEffect);
```

**Android**

В **MainActivity.cs** добавьте следующие строки после `LoadApplication`

```csharp
var x = typeof(Xamarin.Forms.Themes.DarkThemeResources);
x = typeof(Xamarin.Forms.Themes.LightThemeResources);
x = typeof(Xamarin.Forms.Themes.Android.UnderlineEffect);
```


## <a name="related-links"></a>Связанные ссылки

- [Пример ThemesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
