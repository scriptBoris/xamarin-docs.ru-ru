---
title: Специальные возможности на macOS
description: В этом документе описывается работа со средствами специальных возможностей macOS в приложении Xamarin.Mac. Здесь рассматриваются описания элементов пользовательского интерфейса в раскадровках и код, пользовательские элементы управления и тестирования специальных возможностей.
ms.prod: xamarin
ms.assetid: D7F4892B-501A-4271-A7E0-BDD1586B63AD
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: fdda52309ffdb0d32cc42a4dff052cd9050b1e4f
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116619"
---
# <a name="accessibility-on-macos"></a>Специальные возможности на macOS

Эта страница описывает способ использования macOS специальных API-интерфейсы для создания приложений в соответствии с [контрольный список специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md).
Ссылаться на [Android специальных возможностей](~/android/app-fundamentals/accessibility.md) и [специальных возможностей iOS](~/ios/app-fundamentals/accessibility.md) страниц для других API платформы.

Чтобы понять, как доступность API-интерфейсы работают в macOS (прежнее название OS X), сначала ознакомьтесь [модель специальных возможностей OS X](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXmodel.html).

## <a name="describing-ui-elements"></a>Описание элементов пользовательского интерфейса

Использует AppKit `NSAccessibility` протокол, предоставляют API, которые облегчают доступ в пользовательском интерфейсе. Это включает поведение по умолчанию, которое пытается задать значимые значения для свойства специальных возможностей, такие как настройка кнопки `AccessibilityLabel`. Метки обычно является одно слово или короткую фразу, описывающий элемент управления или представлении.

### <a name="storyboard-files"></a>Файлы раскадровки

Xamarin.Mac использует Xcode Interface Builder для изменения файлов раскадровки.
Сведения о специальных возможностях можно изменить в **инспектор удостоверений** при выборе элемента управления в области конструктора (как показано на снимке экрана ниже):

[![Добавление специальных возможностей в Interface Builder в Xcode](accessibility-images/xcode.png "Добавление специальных возможностей в Interface Builder в Xcode")](accessibility-images/xcode-large.png#lightbox)

### <a name="code"></a>Код

Xamarin.Mac не содержит в настоящее время как `AccessibilityLabel` setter.  Добавьте следующий вспомогательный метод, чтобы задать метку специальных возможностей:

```csharp
public static class AccessibilityHelper
{
    [System.Runtime.InteropServices.DllImport (ObjCRuntime.Constants.ObjectiveCLibrary)]
    extern static void objc_msgSend (IntPtr handle, IntPtr selector, IntPtr label);

    static public void SetAccessibilityLabel (this NSView view, string value)
    {
        objc_msgSend (view.Handle, new ObjCRuntime.Selector ("setAccessibilityLabel:").Handle, new NSString (value).Handle);
    }
}
```

Этот метод может затем использоваться в коде, как показано:

```csharp
AccessibilityHelper.SetAccessibilityLabel (someButton, "New Accessible Description");
```

`AccessibilityHelp` Свойство служит для объяснения того, что делает элемент управления или представлении и должны добавляться только если метка может не предоставляет достаточно сведений. Текст справки по-прежнему остаются в короткой, для пример «удаляет документа».

Некоторые элементы пользовательского интерфейса не являются значимыми для доступного доступа (например, метку рядом с входных данных, имеет свои собственные метки специальных возможностей и справки).
В этих случаях установить `AccessibilityElement = false` таким образом, чтобы эти элементы управления или представления будет пропускать средства чтения с экрана или другие специальные возможности.

Компания Apple предоставляет [специальных возможностей](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/EnhancingtheAccessibilityofStandardAppKitControls.html) это объясняет практические рекомендации по доступности меток и текста справки.

## <a name="custom-controls"></a>Пользовательские элементы управления

Ссылаться на Apple [доступных пользовательских элементов управления, касающиеся](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/ImplementingAccessibilityforCustomControls.html) Дополнительные сведения о дополнительных действиях, требуемых.

## <a name="testing-accessibility"></a>Тестирование специальных возможностей

предоставляет macOS **специальных возможностей инспектор** , помогает проверить специальные возможности. Инспектор входит в состав Xcode.

При первом запуске, **специальных возможностей инспектор** потребуется разрешение для управления компьютером с помощью специальных возможностей:

![Инспектор специальных возможностей, запрашивающего разрешение на запуск](accessibility-images/accessibility-inspector-1.png "инспектор специальных возможностей, запрашивающего разрешение на выполнение")

Разблокировать экран параметров (при необходимости, в левом нижнем углу) и деления **специальных возможностей инспектор**:

![Экран параметров, чтобы включить специальные возможности инспектор](accessibility-images/accessibility-inspector-2.png "параметры экрана, чтобы включить инспектор специальных возможностей")

После включения инспектор отображается как плавающее окно, можно перемещать по экрану. На следующем снимке экрана показано инспектор под управлением рядом с Mac пример приложения. Так как курсор перемещается над окном, Инспектор отображает всех доступных свойств каждого элемента управления:

[![Пример выполнения командлета инспектор специальных возможностей](accessibility-images/accessibility-example.png "выполнения инспектора примера специальных возможностей")](accessibility-images/accessibility-example-large.png#lightbox)

Дополнительные сведения в статье [тестирование специальных возможностей для OS X руководство](https://developer.apple.com/library/mac/documentation/Accessibility/Conceptual/AccessibilityMacOSX/OSXAXTestingApps.html).



## <a name="related-links"></a>Связанные ссылки

- [Кросс платформенных специальных возможностей](~/cross-platform/app-fundamentals/accessibility.md)
- [Специальные возможности для Mac](https://www.apple.com/accessibility/mac/)
