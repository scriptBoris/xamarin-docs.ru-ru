---
title: Основы XAML Xamarin.Forms
description: В этом руководстве объясняется, как приступить к работе с XAML платформах для мобильных устройств. XAML позволяет разработчикам определить пользовательские интерфейсы приложений Xamarin.Forms с помощью разметки вместо кода.
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 0f39eb78d46b6156231a165f950f4698e63fc073
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53055745"
---
# <a name="xamarinforms-xaml-basics"></a>Основы XAML Xamarin.Forms

[![Загрузить образец](~/media/shared/download.png) загрузить пример](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)

XAML (расширяемый язык разметки для приложений) позволяет разработчикам определить пользовательские интерфейсы приложений Xamarin.Forms с помощью разметки вместо кода. В приложении Xamarin.Forms XAML никогда не требуется, но часто бывает более емкий и визуально более согласованным, чем эквивалентный код и потенциально оснащен инструментами. XAML особенно хорошо подходит для использования с популярными архитектура приложений MVVM (Model-View-ViewModel): XAML определяет представление, которая связана с ViewModel кода через привязки данных на основе XAML.

## <a name="xaml-basics-contents"></a>Содержимое основы XAML

* [Обзор набора средств Visual Studio для Unity](#Overview)
* [Часть 1. Начало работы с XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [Часть 2. Основной синтаксис XAML](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [Часть 3. Расширения разметки XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [Часть 4. Основы привязки данных](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [Часть 5. От привязки данных к MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

Помимо этих статей основы XAML, вы можете скачать главах книги [Создание мобильных приложений с помощью Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Обложки")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

XAML рассматриваются более подробно в многих главах книги, включая:

<table style="border:0px; box-shadow:0 0px 0px" cellpadding="0" cellspacing="2" border="0" width="85%">
<tr style="background:#ecf0f1">
  <td style="border:0px;">
    <h4>Глава 7. XAML vs. Код</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf">Скачать PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md">Сводка</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Глава 8. Код и XAML в гармонии</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">Скачать PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">Сводка</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Глава 10. Расширения разметки XAML</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf">Скачать PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md">Сводка</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Глава 18. MVVM</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf">Скачать PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md">Сводка</a></td></tr>
</table>

Эти главы может быть [для бесплатной загрузки](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

<a name="Overview" />

## <a name="overview"></a>Обзор

XAML — это язык на основе XML, созданным корпорацией Майкрософт как альтернатива программный код для создания экземпляра и инициализация объектов и организация этих объектов в иерархии родители потомки. XAML, добавлена в несколько технологий в среде .NET framework, но он найден в определении макета пользовательского интерфейса в Windows Presentation Foundation (WPF), Silverlight, среда выполнения Windows и универсальных Windows его наибольшую служебной программы Платформа (UWP).

XAML также является частью Xamarin.Forms, кросс платформенных изначально основе интерфейс программирования для iOS, Android и UWP мобильных устройств. В файле XAML Xamarin.Forms разработчик может определить пользовательские интерфейсы, используя все Xamarin.Forms представлений, макетов и страницы, как также пользовательские классы. XAML-файл можно скомпилировать и внедрен в исполняемый файл. В любом случае XAML информация подвергается синтаксическому анализу во время сборки для поиска именованных объектов и снова во время выполнения для создания экземпляра и инициализации объектов и для установления связи между этими объектами и программный код.

XAML имеет несколько преимуществ, эквивалентный код:

-  XAML чаще всего более емкий, так и удобочитаемыми, чем эквивалентный код.
-  В иерархии родители потомки, присущих XML позволяет XAML для имитации visual многоуровневому стандарту иерархии "родители потомки" объектов пользовательского интерфейса.
-  XAML может быть легко рукописных программистами, но тоже поддается оснащен инструментами, и созданный средствами визуальной разработки.

Конечно существует ряд недостатков, в основном связаны с ограничениями, которые являются внутренними для языков разметки:

-  XAML не может содержать код. Все обработчики событий должны быть определены в файле кода.
-  XAML не может содержать циклы для повторной обработки. (Тем не менее, несколько визуальных объектов Xamarin.Forms — особенно [ `ListView` ](xref:Xamarin.Forms.ListView) — можно создать несколько дочерних элементов, в зависимости от объектов в его `ItemsSource` коллекции.)
-  XAML не может содержать условной обработки (тем не менее, привязку данных может ссылаться преобразователь привязки на основе кода, который фактически позволяет некоторое условную обработку.)
-  Обычно XAML нельзя создавать экземпляры классов, которые не определяют конструктор без параметров. (Тем не менее, есть иногда способ обойти это ограничение.)
-  Как правило, XAML не может вызывать методы. (Опять же, это ограничение можно иногда преодолеть.)

Существует еще не визуальный конструктор для создания XAML в приложениях Xamarin.Forms. Все XAML должен быть написан вручную, но есть [средства предварительного просмотра XAML](~/xamarin-forms/xaml/xaml-previewer.md). Программистам новый XAML может потребоваться часто сборка и запуск приложений, особенно после все, что возможно, очевидно, что неправильно. Даже разработчики богатом опыте в XAML знают, что поощрения для службы "Экспериментирование".

XAML по существу является XML, но некоторые функции уникального синтаксиса XAML. Наиболее важными являются:

- Свойства элементов
- Вложенные свойства
- Расширения разметки

Эти функции являются *не* расширения XML. XAML — вполне допустимо XML. Но эти функции синтаксис XAML использование XML в необычным способом. Они подробно описаны в статьях ниже, которые заканчиваются введение в использование XAML для реализации MVVM.

## <a name="requirements"></a>Требования

В этой статье предполагается, что ознакомиться с Xamarin.Forms. Чтение [введение в Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md) настоятельно рекомендуется.

В этой статье также предполагается, что некоторый опыт работы с XML, включая понимание использования объявления пространства имен XML, а также условия *элемент*, *тега*, и *атрибут*.

Если вы знакомы с Xamarin.Forms и XML, начать чтение [часть 1. Начало работы с XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).



## <a name="related-links"></a>Связанные ссылки

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Введение в Xamarin.Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Создание книги мобильных приложений](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Примеры Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
