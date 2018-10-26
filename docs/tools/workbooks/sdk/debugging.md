---
title: Отладка интеграции
description: В этом документе описывается отладка Xamarin Workbooks интеграции, как на стороне агента, так и на стороне клиента в Windows и Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: lobrien
ms.author: laobri
ms.date: 06/19/2018
ms.openlocfilehash: 86d9c6af93e7f59eb0e819730e46324688df7566
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106004"
---
# <a name="debugging-integrations"></a>Отладка интеграции

## <a name="debugging-agent-side-integrations"></a>Отладка интеграции на стороне агента

Отладка интеграции на стороне агента лучше всего выполняется с помощью методов ведения журнала, предоставляемых `Log` в класс `Xamarin.Interactive.Logging`. См. в разделе [ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/) для методов для вызова.

В Mac OS, сообщения журнала будут отображаться в обоих меню средства просмотра журнала (**окна > средства просмотра журналов**) и в журнале клиента. В Windows появляются только в журнале клиента, так как нет средства просмотра журнала существует.

Журнал клиента находится в следующих расположениях в macOS и Windows:

- MAC: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

Следует учитывать, что при загрузке интеграции через обычные `#r` механизм во время разработки сборки интеграции будут учтены как _зависимостей_ книги и упакованы с ним, если абсолютный путь не используется. Это может привести к изменения вступили в силу не распространение, как если бы перестроение интеграции не происходило никаких действий.

## <a name="debugging-client-side-integrations"></a>Отладка интеграции на стороне клиента

Как написаны на языке JavaScript и загружаются в нашей поверхности обозревателя web интеграции на стороне клиента (см. в разделе [архитектура](~/tools/workbooks/sdk/architecture.md) документации), лучший способ их отладки с помощью средств разработчика WebKit на компьютере Mac, или с помощью средства выбора F12 в Windows .

Оба набора средств позволяют просмотреть исходный код на JavaScript/TypeScript, задавать точки останова, просматривать выходные данные консоли и проверять и изменять модели DOM.

### <a name="mac"></a>Mac

Чтобы включить средства для разработчиков для Xamarin Workbooks на компьютере Mac, выполните следующую команду в окне терминала:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

а затем перезапустите Xamarin Workbooks. После этого вы должны увидеть **проверить элемент** отображаются в вашей контекстного меню, а новые **разработчика** области будут доступны в установках книг. Этот параметр позволяет выбрать, если необходимо, чтобы открыть при запуске средств разработчика:

[![Панель разработчика](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Этот параметр только для перезапуска также — вам потребуется перезапустить клиент книги для ее вступили в силу на книг Excel. Активация средств разработчика с помощью контекстного меню или предпочтений отобразится со знакомым пользовательским Интерфейсом Safari:

[![Средства разработки Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Сведения об использовании средств разработчика Safari, см. в разделе [документации инспектор WebKit][webkit-docs].

### <a name="windows"></a>Windows

В Windows, группы разработки обозревателя IE предоставляет также инструмент под названием «F12 выбора» то есть удаленный отладчик для embedded экземпляры Internet Explorer. Средство можно найти в следующем расположении:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Выбор выполнения F12 чего вы должны увидеть на встроенный экземпляр, лежащих в основе области клиента книги в списке. Выберите его, а также знакомы F12, средства отладки из Internet Explorer будет отображаться, присоединения к клиенту:

[![Инструменты F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
