---
title: Модификаторы поля XAML в Xamarin.Forms
description: 'Атрибут x: FieldModifier указывает уровень доступа для созданных полей для именованных элементов XAML.'
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 7a89735aed9ddf89c855fd33928915d72da40c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36209513"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Модификаторы поля XAML в Xamarin.Forms

_`x:FieldModifier` Атрибут пространства имен указывает уровень доступа для созданных полей для именованных элементов XAML._

## <a name="overview"></a>Обзор

Ниже приведены допустимые значения атрибута.

- `Public` — Указывает, что созданное поле элемента XAML `public`.
- `NotPublic` — Указывает, что созданное поле элемента XAML `internal` на сборку.

Если значение атрибута не задано, будет созданного поля для элемента `private`.

Следующие условия должны быть выполнены для `x:FieldModifier` атрибута должны быть обработаны:

- Элемент верхнего уровня XAML должен быть допустимым `x:Class`.
- Текущий элемент XAML имеет `x:Name` указанного.

Приведенный ниже код XAML показаны примеры присвоение атрибуту.

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> `x:FieldModifier` Атрибут не может использоваться для указания уровня доступа класса XAML.
