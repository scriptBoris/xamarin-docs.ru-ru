---
title: Использовать стандартные библиотеки .NET для совместного использования кода
description: В этом документе описывается, как использовать стандартные библиотеки .NET для совместного использования кода. В нем описывается создание библиотеки .NET Standard, изменение его параметры и использование его в приложении.
ms.prod: xamarin
ms.assetid: 8C30F8D3-1920-453E-9E8B-D40696736FF2
author: conceptdev
ms.author: crdun
ms.date: 07/18/2018
ms.openlocfilehash: 65ba1915a2a968a14f0ce21bcada76e1b83531b0
ms.sourcegitcommit: 46bb04016d3c35d91ff434b38474e0cb8197961b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39270675"
---
# <a name="net-standard-library-code-sharing"></a>Совместное использование кода библиотеки .NET standard

Библиотеки .NET standard имеют унифицированный API для всех платформ .NET, включая Xamarin и .NET Core. Создать единый библиотеки .NET Standard и использовать его из любой среды выполнения, которая поддерживает платформу .NET Standard. Ссылаться на [на этой диаграмме](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support) подробные сведения о поддерживаемых платформах.

.NET Standard версий с 1.0 до версии 1.6 обеспечивают постепенно большего подмножества платформы .NET Framework, .NET Standard 2.0 предоставляет оптимальный уровень поддержки для приложений Xamarin и перенос существующих переносимые библиотеки классов.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

## <a name="visual-studio-for-mac"></a>Visual Studio для Mac

В этом разделе описано, как создать и использовать библиотеку .NET Standard, с помощью Visual Studio для Mac.

### <a name="creating-a-net-standard-library"></a>Создание библиотеки .NET Standard

Добавление библиотеки .NET Standard в решение является достаточно прост.

1. В **Добавление нового проекта** диалоговом окне выберите **.NET Core** категории, а затем выберите **библиотеки .NET Standard**:

    ![Создать библиотеку .NET Standard](net-standard-images/vsm01-m157.png "Создание библиотеки новом .NET Standard")

2. На следующем экране выберите целевую платформу, - **.NET Standard 2.0** рекомендуется:

    [![Выберите .NET Standard 2.0](net-standard-images/vsm01a-m157-sml.png)](net-standard-images/vsm01a-m157.png#lightbox)

3. На последнем экране, введите имя проекта и нажмите кнопку **создать**.

4. Проект библиотеки .NET Standard будет отображаться, как показано в обозревателе решений. Узел зависимостей укажет, что библиотека использует [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![Указывает узел зависимостей в решении, .NET Standard](net-standard-images/vsm02-m157.png)

#### <a name="editing-net-standard-library-settings"></a>Изменение параметров библиотеки .NET Standard

Параметры библиотеки .NET Standard можно просмотреть и, щелкнув правой кнопкой мыши на проект и выбрав команду `Options` как показано на следующем снимке экрана:

![.NET Standard целевой платформы в параметрах проекта изменять](net-standard-images/vsm03-m157.png "изменить версию .NET Standard целевой платформы в параметрах проекта")

Внутри можно изменить версию `netstandard` , изменив `Target Framework` значение в раскрывающемся списке.

**Кроме того:** можно изменить `.csproj` напрямую, чтобы изменить это значение.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="visual-studio-2017-windows"></a>Visual Studio 2017 (Windows)

В этом разделе описано, как создать и использовать библиотеку .NET Standard, с помощью Visual Studio.

### <a name="creating-a-net-standard-library"></a>Создание библиотеки .NET Standard

Добавление библиотеки .NET Standard в решение является достаточно прост.

1. В **новый проект** диалоговом окне выберите **.NET Standard** категории, а затем выберите **библиотека классов (.NET Standard)**.

    ![Создание новой библиотеки классов .NET Standard](net-standard-images/vs01-w157.png "Создание новой библиотеки классов .NET Standard")

2. Проект библиотеки .NET Standard будет отображаться, как показано в обозревателе решений. Узел зависимостей укажет, что библиотека использует [NETStandard.Library](https://www.nuget.org/packages/NETStandard.Library/).

    ![NETStandard.Library в папке проекта](net-standard-images/vs02-w157.png "проект .NET Standard в решении")

### <a name="editing-net-standard-library-settings"></a>Редактирование .NET Standard параметры библиотеки

Параметры библиотеки .NET Standard можно просмотреть и, щелкнув правой кнопкой мыши на проект и выбрав команду **свойства** как показано на следующем снимке экрана:

![Изменение стандартных целевых платформ .NET в свойствах проекта](net-standard-images/vs03-w157.png "так же, как другие проекты ссылаются на библиотеку .NET Standard")

**Кроме того:** можно изменить `.csproj` непосредственно для редактирования `TargetFramework` элемент и изменений, какая из версий лучше целевыми (например) `<TargetFramework>netstandard2.0</TargetFramework>`).

### <a name="using-a-net-standard-library-project"></a>С использованием проекта библиотеки .NET Standard

После создания библиотеки .NET Standard, можно добавить ссылку на него из любой проектов, совместимый с приложения или библиотеки таким же образом, обычно добавляются ссылки. В Visual Studio, щелкните правой кнопкой мыши на узел "ссылки" и выберите **добавить ссылку...**  переключитесь **проекты > решение** как показано:

![Ссылки на библиотеку .NET Standard](net-standard-images/vs04.png "в Visual Studio щелкните правой кнопкой мыши на узел \"ссылки\" и выберите команду Добавить ссылку …, затем перейдите на вкладку проекты решения, как показано")

-----

## <a name="related-links"></a>Связанные ссылки

* [.NET standard](https://docs.microsoft.com/dotnet/standard/net-standard) -подробные сведения и сравнение переносимую библиотеку классов.
