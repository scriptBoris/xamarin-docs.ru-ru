---
title: Отладка интеграции
description: Этот документ описывает отладку интеграции Xamarin книг, на стороне клиента и стороне клиента в Windows и Mac.
ms.prod: xamarin
ms.assetid: 90143544-084D-49BF-B44D-7AF943668F6C
author: topgenorth
ms.author: toopge
ms.date: 06/19/2018
ms.openlocfilehash: 6e37b1ac3d0fb78b5737ebe97b5a28ab40adb648
ms.sourcegitcommit: d70fcc6380834127fdc58595aace55b7821f9098
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36269061"
---
# <a name="debugging-integrations"></a>Отладка интеграции

## <a name="debugging-agent-side-integrations"></a>Отладка интеграции на стороне клиента

Отладку на стороне клиента интеграции лучше всего проводить с помощью ведения журнала методы, предоставляемые `Log` в класс `Xamarin.Interactive.Logging`. В разделе [ `API docs` ](https://developer.xamarin.com/api/type/Xamarin.Interactive.Logging.Log/) для метода для вызова.

На macOS, сообщения журнала отображаются в обоих меню средства просмотра журнала (**окна > средства просмотра журналов**) и в журнал клиента. В Windows появляются только в журнале клиента как не существует просмотра журнала.

Журнал клиента находится в следующих местоположениях в macOS и Windows:

- MAC: `~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

Следует иметь в виду, что при загрузке интеграции через обычные `#r` механизм во время разработки сборки интеграции будет выбрано как _зависимостей_ книги и если абсолютный путь в пакеты с ним не используется. Это может вызвать изменения в по-видимому не распространяются, как если бы перестроение интеграции не происходило никаких действий.

## <a name="debugging-client-side-integrations"></a>Отладка клиентского интеграции

Как пишутся на языке JavaScript и загружаются в нашей область обозревателя web интеграции на стороне клиента (см. [архитектура](~/tools/workbooks/sdk/architecture.md) документации), лучший способ выполнить отладку с помощью средств разработчика WebKit на Mac, или с помощью выбора F12 в Windows .

Оба набора инструментов позволяют просмотреть исходный код JavaScript или TypeScript, установите точки останова, просмотр выходных данных консоли и проверять и изменять модели DOM.

### <a name="mac"></a>Mac

Чтобы включить средства разработчика для книг Xamarin на Mac, выполните следующую команду в окне терминала:

```shell
defaults write com.xamarin.Workbooks WebKitDeveloperExtras -bool true
```

а затем перезапустите Xamarin книги. После этого вы увидите **проверить элемент** отображаются в вашей контекстного меню и новый **разработчика** области будут доступны в установках книг. Этот параметр позволяет выбрать, должны ли открываться при запуске средств разработчика:

[![Панель разработчика](debugging-images/developer-pane-small.png)](debugging-images/developer-pane.png#lightbox)

Этот параметр также только для перезапуска, необходимо будет перезапустить клиент книги для ее вступили в силу на новых книг. Активация через контекстное меню или настройки средств разработчика отобразится знакомый пользовательский Интерфейс обозревателя Safari:

[![Средства разработки Safari](debugging-images/mac-dev-tools.png)](debugging-images/mac-dev-tools.png#lightbox)

Сведения об использовании средств разработчика Safari см. в разделе [документации инспектора WebKit][webkit-docs].

### <a name="windows"></a>Windows

В Windows, команда IE предоставляет средство называется «F12 выбора», удаленный отладчик для внедренных экземпляров Internet Explorer. Инструмент можно найти в следующем расположении:

```shell
C:\Windows\System32\F12\F12Chooser.exe
```

Выбор выполнения F12 и увидеть внедренного экземпляра, лежащих в основе области клиента книги в списке. Выберите и знакомые F12, будет отображаться средства отладки из Internet Explorer, присоединен к клиенту.

[![Инструменты F12](debugging-images/windows-dev-tools.png)](debugging-images/windows-dev-tools.png#lightbox)

[webkit-docs]: https://trac.webkit.org/wiki/WebInspector
