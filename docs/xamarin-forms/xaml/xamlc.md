---
title: Компиляция XAML в Xamarin.Forms
description: В этой статье объясняется, как XAML можно при необходимости компилируется непосредственно в промежуточный язык (IL) с помощью компилятора Xamarin.Forms XAML (XAMLC).
ms.prod: xamarin
ms.assetid: 9A2D10A6-5DFC-485F-A75A-2F7B98314025
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 07/02/2018
ms.openlocfilehash: b828e62ef1037bf47a2ae5fb303fbf8fcace6549
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38997137"
---
# <a name="xaml-compilation-in-xamarinforms"></a>Компиляция XAML в Xamarin.Forms

_Можно при необходимости скомпилировать XAML напрямую в промежуточный язык (IL) с помощью компилятора XAML (XAMLC)._

Компилятор XAMLC предлагает ряд преимуществ, которые перечислены далее:

- Он проверяет XAML во время компиляции, уведомляя пользователя об ошибках.
- Он сокращает часть времени загрузки и создания элементов XAML.
- Он позволяет сократить размер файла окончательной сборки, так как больше не добавляет XAML-файлы.

XAMLC отключен по умолчанию для обеспечения обратной совместимости. Его можно включить на уровне класса и сборки, добавив `XamlCompilation` атрибута.

В следующем примере кода показано включение XAMLC на уровне сборки:

```csharp
using Xamarin.Forms.Xaml;
...
[assembly: XamlCompilation (XamlCompilationOptions.Compile)]
namespace PhotoApp
{
  ...
}
```

В этом примере проверку всех XAML, содержатся внутри сборки во время компиляции будет выполняться, с ошибками XAML, сообщаемый во время компиляции, а не во время выполнения. Таким образом `assembly` префикс для `XamlCompilation` атрибут указывает, что атрибут применяется ко всей сборке.

В следующем примере кода показано включение XAMLC на уровне класса:

```csharp
using Xamarin.Forms.Xaml;
...
[XamlCompilation (XamlCompilationOptions.Compile)]
public class HomePage : ContentPage
{
  ...
}
```

В этом примере XAML для проверки во время компиляции `HomePage` класс будет выполнена, и выводятся сообщения об ошибках в ходе процесса компиляции.

> [!NOTE]
> `XamlCompilation` Атрибут и `XamlCompilationOptions` перечисления находятся в `Xamarin.Forms.Xaml` пространство имен, которое необходимо импортировать их использования.


## <a name="related-links"></a>Связанные ссылки

- [XamlCompilation](xref:Xamarin.Forms.Xaml.XamlCompilationAttribute)
- [XamlCompilationOptions](xref:Xamarin.Forms.Xaml.XamlCompilationOptions)
