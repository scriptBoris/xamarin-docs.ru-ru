---
title: Сводка Глава 6. Нажатие кнопки
description: 'Создание мобильных приложений с помощью Xamarin.Forms: Сводка Глава 6. Нажатие кнопки'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 0f1da94031e658d42205e6346d41b02c5822d992
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563684"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Сводка Глава 6. Нажатие кнопки

[ `Button` ](xref:Xamarin.Forms.Button) — Это представление, которое позволяет пользователю запускать команду. Объект `Button` идентифицируется по тексту (и при необходимости изображением, как показано в [главе 13, точечные рисунки](chapter13.md)). Следовательно `Button` определяет многие из тех же свойств `Label`:

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` также определяет три свойства определяют внешний вид его границу, но эти свойства и их взаимной независимость поддерживается только конкретной платформе:

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) типа `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) типа `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) типа `Double`

`Button` также наследует все свойства `VisualElement` и `View`, в том числе `BackgroundColor`, `HorizontalOptions`, и `VerticalOptions`.

## <a name="processing-the-click"></a>Обработка щелчка

`Button` Класс определяет [ `Clicked` ](xref:Xamarin.Forms.Button.Clicked) событие, возникающее, когда пользователь касается `Button`. `Click` Обработчика имеет тип `EventHandler`. Первым аргументом является `Button` объект, генерирующий событие; второй аргумент — `EventArgs` объект, предоставляющий дополнительные сведения отсутствуют.

[ **ButtonLogger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) в нем демонстрируется простой `Clicked` обработки.

## <a name="sharing-button-clicks"></a>Нажимает кнопку общего доступа к кнопке

Несколько `Button` представления не могут иметь одинаковый `Clicked` обработчик, но обработчик обычно требуется определить, какие `Button` отвечает за определенное событие. Одним из подходов является для сохранения различных `Button` объектов в виде полей и проверьте, какой из них он генерирует события в обработчик.

[ **TwoButtons** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) этот метод продемонстрирован в примере. Программа также демонстрирует, как задать [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) свойство `Button` для `false` при нажатии клавиши `Button` больше не является допустимым. Отключенный `Button` не создает `Clicked` событий.

## <a name="anonymous-event-handlers"></a>Обработчики событий анонимный

Это можно определить `Clicked` обработчиками в качестве анонимного лямбда-функции, как [ **ButtonLambdas** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) в нем демонстрируется. Тем не менее анонимные обработчики не может использоваться без кода запутанным отражения.

## <a name="distinguishing-views-with-ids"></a>Отличительный признак представления с идентификаторами

Несколько `Button` объектов также различаются, задав [ `StyleId` ](xref:Xamarin.Forms.Element.StyleId) свойство или [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId) свойства `string`. Это свойство определяется `Element` , но он не используется в Xamarin.Forms. Он предназначен для использования исключительно с приложениями.

[ **SimplestKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) образец использует тот же обработчик событий для всех 10 клавиш с цифрами на цифровой клавиатуре и различать их с помощью `StyleId` свойство:

[![Тройной снимок простейший клавиатуре](images/ch06fg04-small.png "калькулятор")](images/ch06fg04-large.png#lightbox "калькулятора")

## <a name="saving-transient-data"></a>Сохранение временных данных

Во многих приложениях необходимые для сохранения данных, когда программа будет завершена и перезагрузить этот данные после повторного запуска программы. [ `Application` ](xref:Xamarin.Forms.Application) Класс определяет несколько членов, которые позволить программе сохранять и восстанавливать временных данных:

- [ `Properties` ](xref:Xamarin.Forms.Application.Properties) Свойство — это словарь с `string` ключи и `object` элементов. Содержимое словаря, автоматически сохраняются в локальном хранилище приложения перед завершением работы программы и загружаются в память при запуске программы.
- `Application` Класс определяет три защищенных виртуальных методов, что программа стандартный `App` класса переопределения: [ `OnStart` ](xref:Xamarin.Forms.Application.OnStart), [ `OnSleep` ](xref:Xamarin.Forms.Application.OnSleep), и [ `OnResume` ](xref:Xamarin.Forms.Application.OnResume). Они относятся к *жизненного цикла приложения* события.
- [ `SavePropertiesAsync` ](xref:Xamarin.Forms.Application.SavePropertiesAsync) Метод сохраняет содержимое словаря.

Нет необходимости вызывать `SavePropertiesAsync`. Содержимое словаря, автоматически сохраняются перед завершением работы программы и происходит до запуска программы. Это полезно во время тестирования программы для сохранения данных в том случае, если программа завершает работу.

Также полезен:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), статическое свойство, которое возвращает текущий `Application` объект, который затем можно использовать для получения `Properties` словаря.

Первый шаг — определить все переменные страницы, которые нужно сохранить при завершении работы программы. Если вы знаете все места, где изменение этих переменных, можно просто добавить их к `Properties` словаря в этот момент. В конструктор страницы, можно задать переменные из `Properties` словарь, если ключ существует.

Более крупной программы, возможно, потребуется иметь дело с событиями жизненного цикла приложения. Наиболее важным является `OnSleep` метод. Вызов этого метода указывает, что программа покинул переднего плана. Возможно, пользователь нажал **Главная** кнопку на устройстве, или отображаются все приложения или завершает работу по телефону. Вызов `OnSleep` только уведомление о том, что программа получит до его отключения. Программа должна воспользоваться данной возможностью, чтобы убедиться, что `Properties` словаря в актуальном состоянии.

Вызов `OnResume` указывает, что программа не завершился после последнего вызова `OnSleep` , однако теперь выполняется на переднем плане еще раз. Программа может использовать эту возможность для обновления подключения к Интернету (к примеру).

Вызов `OnStart` происходит во время запуска программы. Нет необходимости ждать, пока вызов этого метода доступа `Properties` словарь поскольку содержимое уже были восстановлены при `App` вызывается конструктор.

[ **PersistentKeypad** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) пример очень похожа на **SimplestKeypad** за исключением того, что программа использует `OnSleep` переопределение, чтобы сохранить текущую сущность клавиатуре, и Конструктор страниц для восстановления данных.

> [!NOTE]
> Другой подход к сохранению параметров программы предоставляется Xamarin.Essentials [предпочтения](~/essentials/preferences.md) класса.

## <a name="related-links"></a>Связанные ссылки

- [Глава 6 полнотекстового поиска (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Примеры Глава 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Глава 6 F# примеры](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Кнопка Xamarin.Forms](~/xamarin-forms/user-interface/button.md)
