---
title: Задание стиля приложений Xamarin.Forms с помощью стилей XAML
description: В этом руководстве объясняется, как настроить внешний вид приложения Xamarin.Forms с помощью стилей XAML.
ms.prod: xamarin
ms.assetid: 344A34AA-B19A-4765-BC8A-875D9A6B5EA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: ec41955ac15ab23579a5e63b9e17eed61a74e86f
ms.sourcegitcommit: 817d26585093cd180a36b28179eb354b0eb900b3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55291990"
---
# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Задание стиля приложений Xamarin.Forms с помощью стилей XAML

## <a name="introductionintroductionmd"></a>[Введение](introduction.md)

Приложения Xamarin.Forms часто содержат несколько элементов управления, имеющих идентичным внешним видом. Настройка внешнего вида каждого отдельного элемента управления может быть повторяющихся и подвержено ошибкам. Вместо этого стили можно создавать, настраивать внешний вид элемента управления путем группирования и задание свойств, доступных на типе элемента управления.

## <a name="explicit-stylesexplicitmd"></a>[Явные стили](explicit.md)

*Явные* стиль — это приложения, выборочно применять к элементам управления, установив их [ `Style` ](xref:Xamarin.Forms.VisualElement.Style) свойства.

## <a name="implicit-stylesimplicitmd"></a>[Неявные стили](implicit.md)

*Неявное* стиля, которое используется для всех элементов управления в одной и той же [ `TargetType` ](xref:Xamarin.Forms.Style.TargetType), не требуя от каждого элемента управления для ссылки на стиль.

## <a name="global-stylesapplicationmd"></a>[Глобальные стили](application.md)

Стили можно сделать доступными глобально, добавив их в приложение [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary). Это помогает избежать дублирования стилей всех страниц или элементов управления.

## <a name="style-inheritanceinheritancemd"></a>[Наследование стилей](inheritance.md)

Стили можно наследовать другие стили, чтобы сократить дублирование и включить повторное использование.

## <a name="dynamic-stylesdynamicmd"></a>[Динамические стили](dynamic.md)

Стили не отвечать на изменения свойств и остаются неизменными в течение всего приложения. Тем не менее приложения могут реагировать на изменения стиля динамически во время выполнения с помощью динамических ресурсов.

## <a name="device-stylesdevicemd"></a>[Стили устройства](device.md)

Платформа Xamarin.Forms включает шесть *динамическое* стили, известный как *устройства* стили, в [ `Devices.Styles` ](xref:Xamarin.Forms.Device.Styles) класса. Все шесть стили могут применяться к [ `Label` ](xref:Xamarin.Forms.Label) только экземпляр.

## <a name="style-classesstyle-classmd"></a>[Классы стилей](style-class.md)

Классы стилей Xamarin.Forms включить несколько стилей, применяемых к элементу управления, не прибегая к наследования стиля.
