---
title: Xamarin.Forms темы
description: В этой статье описаны Xamarin.Forms темы, которые определяют конкретных визуальное представление для стандартных представлений.
ms.prod: xamarin
ms.assetid: 3DFB7C55-69F6-4980-A501-588719143482
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 0f49eeba072d6aeb7ead40d5d56d4af9e9bf5e27
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35245736"
---
# <a name="xamarinforms-themes"></a>Xamarin.Forms темы

![](~/media/shared/preview.png "Этот API в настоящее время находится в предварительной версии")

Xamarin.Forms темы были объявлены в Evolve 2016 и доступны для просмотра клиентов повторите и предоставлять отзывы.

Тема добавляется к приложению Xamarin.Forms, включая **Xamarin.Forms.Theme.Base** пакета Nuget, а также дополнительный пакет, который определяет конкретной темы (например) Xamarin.Forms.Theme.Light) либо иначе локального темы можно задать для приложения.

См. [Светлая тема](light.md) и [темной темой](dark.md) страниц для инструкции о способах добавления их в приложение или извлечь [примере пользовательскую тему](custom.md).

**Важно:** необходимо также выполнить шаги для [загружать темы сборки (см. ниже)](#loadtheme) , добавив некоторые стандартный код для iOS `AppDelegate` и Android `MainActivity`. Это будет улучшена в будущем предварительного выпуска.


## <a name="control-appearance"></a>Внешний вид элемента управления

[Свет](light.md) и [темной](dark.md) темы оба определяют определенный внешний вид для стандартных элементов управления. После добавления в словарь ресурсов приложения темы, изменится вида стандартных элементов управления.

Приведенная ниже разметка XAML показаны некоторые общие элементы управления:

```xaml
<StackLayout Padding="40">
    <Label Text="Regular label" />
    <Entry Placeholder="type here" />
    <Button Text="OK" />
    <BoxView Color="Yellow" />
    <Switch />
</StackLayout>
```

Эти снимки экрана отображение этих элементов управления с:

* Тема не применяется
* Светлая тема (только небольшие отличия, что без темы)
* Темная тема

![](images/standard-none-sml.png "Элементы управления без использования тем") ![](images/standard-light-sml.png "элементы управления со \"светлой\" теме") ![](images/standard-dark-sml.png "элементов управления с \"темной\" теме")

<a name="styleclass" />

## <a name="styleclass"></a>StyleClass

`StyleClass` Свойство позволяет внешний вид представления должен быть изменен в соответствии с определением, предоставляемые темы.

[Свет](light.md) и [темной](dark.md) три различные варианты отображения для определения темы обоих `BoxView`: `HorizontalRule`, `Circle`, и `Rounded`. Эта разметка показаны три различных `BoxView`s с классами другой стиль применен:

```xaml
<StackLayout Padding="40">
    <BoxView StyleClass="HorizontalRule" />
    <BoxView StyleClass="Circle" />
    <BoxView StyleClass="Rounded" />
</StackLayout>
```

Это делает со светлой и темной следующим образом:

![](images/boxview-light-sml.png "BoxView с StyleClass Светлая тема") ![](images/boxview-dark-sml.png "BoxView с StyleClass \"темной\" теме")

<a name="builtin" />

## <a name="built-in-classes"></a>Встроенные классы

Помимо автоматически стиля распространенные управляет светлой и темной темы в настоящее время поддерживает следующие классы, которые могут быть применены, задав `StyleClass` для этих элементов управления:

**BoxView**

* HorizontalRule
* Круг
* Округленное

**Изображение**

* Круг
* Округленное
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
* Обратное


## <a name="troubleshooting"></a>Устранение неполадок

<a name="loadtheme" />

### <a name="could-not-load-file-or-assembly-xamarinformsthemelight-or-one-of-its-dependencies"></a>Не удалось загрузить файл или сборку «Xamarin.Forms.Theme.Light» или одну из ее зависимостей

В предварительном выпуске темы может быть не удалось загрузить во время выполнения. Добавьте код, показанный ниже, в соответствующие проекты для устранения этой ошибки.

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

- [Образец ThemesDemo](https://github.com/xamarin/xamarin-forms-samples/tree/master/Themes/ThemesDemo)
