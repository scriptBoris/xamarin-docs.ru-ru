---
title: Известные проблемы и обходные пути
description: В этом документе описаны известные проблемы и решения для Xamarin Workbooks. В нем описывается CultureInfo, JSON проблем и многое другое.
ms.prod: xamarin
ms.assetid: 495958BA-C9C2-4910-9BAD-F48A425208CF
author: topgenorth
ms.author: toopge
ms.date: 03/30/2017
ms.openlocfilehash: d362698d2844ae6d96bba4929d509f5373742578
ms.sourcegitcommit: aa9b9b203ab4cd6a6b4fd51e27d865e2abf582c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39350999"
---
# <a name="known-issues--workarounds"></a>Известные проблемы и обходные пути

## <a name="persistence-of-cultureinfo-across-cells"></a>Сохраняемость CultureInfo в ячейках

Установка `System.Threading.CurrentThread.CurrentCulture` или `System.Globalization.CultureInfo.CurrentCulture` не сохраняются между ячейках книги о целевых показателях книг на основе Mono (Mac, iOS и Android) из-за [ошибку в Mono `AppContext.SetSwitch` ] [ appcontext-bug] реализации .

### <a name="workarounds"></a>Методы обхода проблемы

* Задать приложение домена local `DefaultThreadCurrentCulture`:
```csharp
using System.Globalization;
CultureInfo.DefaultThreadCurrentCulture = new CultureInfo("de-DE")
```

* Или обновление до книг 1.2.1 или более поздней версии, что перепишет назначения `System.Threading.CurrentThread.CurrentCulture` и `System.Globalization.CultureInfo.CurrentCulture` для обеспечения требуемого поведения (временное решение Mono ошибки).

## <a name="unable-to-use-newtonsoftjson"></a>Не удается использовать Newtonsoft.Json

### <a name="workaround"></a>Обходной путь

* Обновление до книг 1.2.1, которые будут устанавливать Newtonsoft.Json 9.0.1.
  Книги 1.3, в настоящее время в альфа-канал поддерживает версии 10 и более поздних версиях.

### <a name="details"></a>Подробные сведения

Newtonsoft.Json 10 была выпущена которой уменьшил ее зависимость от Microsoft.CSharp, который конфликтует с книгами версии поставляется для поддержки `dynamic`. Эта проблема устранена в предварительной версии 1.3 книг, но сейчас мы обойти это, закрепляющих Newtonsoft.Json специально для версии 9.0.1.

Пакеты NuGet, явным образом в зависимости от Newtonsoft.Json 10 или более поздней версии поддерживаются только в версии 1.3 книг, в настоящее время в альфа-канала.

## <a name="code-tooltips-are-blank"></a>Код подсказки должны пусто

Существует [ошибки в редакторе Монако] [ monaco-bug] в Safari/WebKit, который используется в приложении книги по Mac, результаты при подготовке к просмотру подсказки кода без текста.

![](general-images/monaco-signature-help-bug.png)

### <a name="workaround"></a>Обходной путь

* Щелкнув всплывающей подсказки, после его появления вынудит текст для отображения.

* Или обновление до книг 1.2.1 или более поздней версии

[appcontext-bug]: https://bugzilla.xamarin.com/show_bug.cgi?id=54448
[monaco-bug]: https://github.com/Microsoft/monaco-editor/issues/408

## <a name="skiasharp-renderers-are-missing-in-workbooks-13"></a>В версии 1.3 книги отсутствуют SkiaSharp модулей подготовки отчетов

Начиная с версии 1.3 книги, мы удалили SkiaSharp модулей подготовки отчетов, которые мы выпустили в книгах 0.99.0, пользу SkiaSharp предоставления визуализаторов, самой, с помощью наших [SDK](~/tools/workbooks/sdk/index.md).

### <a name="workaround"></a>Обходной путь

* Обновите SkiaSharp до последней версии в NuGet. Во время написания этой статьи это 1.57.1.

## <a name="related-links"></a>Связанные ссылки

- [Сообщения об ошибках](~/tools/workbooks/install.md#reporting-bugs)
