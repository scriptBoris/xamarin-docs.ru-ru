---
title: 'Почему приложение iOS 9 завершается ошибкой: System.Exception: не удалось маршалировать объект Objective-C?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8805ABEC-48D4-4CCB-A226-3A5B2ECE4BF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: dfe1918d271946eb96d1f57d32c533a075f6d0bd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50103365"
---
# <a name="why-does-my-ios-9-app-fail-with-systemexception-failed-to-marshal-the-objective-c-object"></a>Почему приложение iOS 9 завершается ошибкой: System.Exception: не удалось маршалировать объект Objective-C?

Может появиться ошибка этой формы:

> System.Exception: Не удалось маршалировать объект Objective-C... Для этого объекта не удалось найти существующий управляемый экземпляр...

Изменения API в iOS 9 требуют, что конструктор обратного вызова можно использовать при вызов неуправляемого кода, в качестве базового интерфейса API теперь ожидает ее. Добавление обратного вызова конструктора класса, используйте следующую строку: 

`public foo (IntPtr handle) : base (handle) ` 

### <a name="next-steps"></a>Следующие шаги

Дополнительная помощь, свяжитесь с нами, или если эта проблема остается даже после использования указанные выше сведения, ознакомьтесь с разделом [какие варианты поддержки доступны для Xamarin?](~/cross-platform/troubleshooting/support-options.md) сведения о параметрах связи, предложения, а также как файл новую ошибку, при необходимости. 
