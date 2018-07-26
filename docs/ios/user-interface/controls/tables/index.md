---
title: Работа с таблицами и ячейками в Xamarin.iOS
description: Этот документ содержит ссылки на различные руководства, описывающие способ отображения данных с помощью элемента управления UITableView в приложении Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 04DF47DD-4E17-75D7-AC7C-8CF4A574CD21
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 01/06/2016
ms.openlocfilehash: ea5b6ba532d577bd503529065eef803acf3a7aa9
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39241702"
---
# <a name="working-with-tables-and-cells-in-xamarinios"></a>Работа с таблицами и ячейками в Xamarin.iOS

В этом разделе представлены классы, используемые для создания и отображения таблиц, а затем приводятся примеры их использования в Xamarin.iOS. Здесь рассматриваются, используя внешний вид по умолчанию для таблиц, Настройка макета, реализация редактирования и использовании конструктора iOS Xamarin для создания таблицы в визуально. Иногда отображение очевидно, список строк (например, приложение Music) и в других случаях это трудно распознаваемых управления таблицы (например, для редактирования в приложении контактов или диалога в приложение "сообщения").

Для тех, работа на кросс платформенных приложений с помощью Xamarin.Android управления UITableView аналогичен классу ListView в Android (и класс UITableViewSource аналогичен классы адаптеров Android).

Эти статьи займет исчерпывающие работа с таблицами, в том числе:

-   **Таблицы частей** — введение и объясняющий визуальные элементы в `UITableView` элемента управления. 
-   **Отображение данных в таблицах** — в котором показано, как создать и заполнить таблицу данными, использовать различные стили таблиц и ячеек и избежать проблем с памятью, повторное использование объектов ячейки. 
-   **Расширенное использование** — для построения пользовательских ячеек, так и для использования функций редактирования UITableView класса. 
-   **Создание таблицы в визуально** — с помощью конструктора Xamarin для iOS для создания интерфейса на основе таблицы с помощью раскадровки. 

## <a name="contents"></a>Описание

 [Таблицы частей &amp; функциональные возможности](~/ios/user-interface/controls/tables/table-parts-and-functionality.md)

 [Заполнение таблицы данными](~/ios/user-interface/controls/tables/populating-a-table-with-data.md)

 [Настройка вида таблицы](~/ios/user-interface/controls/tables/customizing-table-appearance.md)

 [Редактирование](~/ios/user-interface/controls/tables/editing.md)
 
 [Действия строки](~/ios/user-interface/controls/tables/row-action.md)

 [Создание таблиц в раскадровке](~/ios/user-interface/controls/tables/creating-tables-in-a-storyboard.md)
 
 [Автоматическое изменение высоты строки](~/ios/user-interface/controls/tables/autosizing-row-height.md)

## <a name="related-links"></a>Связанные ссылки

- [WorkingWithTables (пример)](https://developer.xamarin.com/samples/monotouch/WorkingWithTables/)
- [Таблицы в раскадровках (пример)](https://developer.xamarin.com/samples/monotouch/StoryboardTable/)
- [Введение в раскадровку](~/ios/user-interface/storyboards/index.md)
- [Рецепт TableView раскадровки](https://github.com/xamarin/recipes/tree/master/Recipes/ios/general/storyboard/storyboard_a_tableview)
- [Введение в контроллер представления MonoTouch.Dialog](~/ios/user-interface/monotouch.dialog/index.md)
- [Пример TableEditing на Github](https://github.com/xamarin/monotouch-samples/tree/master/TableEditing)
- [Пример TableParts на Github](https://github.com/xamarin/monotouch-samples/tree/master/TableParts)
- [Пример TableAndCellStyles на Github](https://github.com/xamarin/mobile-samples/tree/master/TablesLists)
- [Ссылки на класс UITableView](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableView_Class/)
- [Ссылки на класс UITableViewCell](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewCell_Class/)
- [UITableViewDelegate](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDelegate_Protocol/)
- [UITableViewDataSource](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITableViewDataSource_Protocol/)
