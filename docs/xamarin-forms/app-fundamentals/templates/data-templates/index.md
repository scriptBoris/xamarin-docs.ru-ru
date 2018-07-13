---
title: Шаблоны Xamarin.Forms данных
description: DataTemplate используется для определения внешнего вида данных в поддерживаемых элементах управления и обычно привязывается к данным для отображения.
ms.prod: xamarin
ms.assetid: 838F4BDB-B719-457F-8633-27E9B267A2A0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 771ae22c3e28a4fce758bbfd6a3bd63bafb75e53
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38994975"
---
# <a name="xamarinforms-data-templates"></a>Шаблоны Xamarin.Forms данных

_DataTemplate используется для определения внешнего вида данных в поддерживаемых элементах управления и обычно привязывается к данным для отображения._

## <a name="introductionintroductionmd"></a>[Введение](introduction.md)

Шаблоны Xamarin.Forms данных предоставляют возможность настраивать представление данных в поддерживаемых элементах управления. В этой статье введение в шаблоны данных, анализ, почему они необходимы.

## <a name="creating-a-datatemplatecreatingmd"></a>[Создание шаблона данных DataTemplate](creating.md)

Шаблоны данных могут создаваться встроенный в [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), или из пользовательского типа или соответствующий тип ячейки Xamarin.Forms. Встроенный шаблон следует использовать, если нет необходимости повторно использовать шаблон данных в другом месте. Кроме того шаблон данных могут использоваться повторно, определив его как пользовательский тип, или как ресурс управления уровня, уровня страницы или уровня приложения.

## <a name="creating-a-datatemplateselectorselectormd"></a>[Создание DataTemplateSelector](selector.md)

Объект [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) может использоваться для выбора [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) во время выполнения на основе значения свойства с привязкой к данным. Это позволяет нескольким `DataTemplate` экземпляров для применения к тому же типу объектов, чтобы настроить внешний вид определенных объектов. В этой статье показано, как создавать и использовать `DataTemplateSelector`.


## <a name="related-links"></a>Связанные ссылки

- [Шаблоны данных (пример)](https://developer.xamarin.com/samples/xamarin-forms/templates/datatemplates/)
