---
title: Функции платформы Xamarin.Forms
description: В этом руководстве объясняется, как воспользоваться преимуществами функций платформы приложений Xamarin.Forms с помощью разных методов.
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/08/2018
ms.openlocfilehash: 3f0156926f8d7a31e2e80318d7b05a909f158653
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207730"
---
# <a name="xamarinforms-platform-features"></a>Функции платформы Xamarin.Forms

Xamarin.Forms расширяется и позволяет объединять функций конкретных платформ, с помощью [эффекты](~/xamarin-forms/app-fundamentals/effects/index.md), [пользовательские отрисовщики](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md), [Помощью MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)и многое другое.

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

В этом руководстве описывается Android особенностей платформы Xamarin.Forms и как реализовать материальному дизайну, обновив имеющихся приложений Xamarin.Forms Android.

## <a name="device-classdevicemd"></a>[Класс устройства](device.md)

Как использовать `Device` класс для создания поведения конкретной платформы в общий код и пользовательский интерфейс (в том числе с помощью XAML). Также рассматривается `BeginInvokeOnMainThread` что важно при изменении элементов управления пользовательского интерфейса из фоновых потоков.

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

В этом руководстве описаны iOS особенностей платформы Xamarin.Forms, и способы выполнения дополнительных операций ввода-вывода стиля с помощью **Info.plist** и `UIAppearance` API.

## <a name="native-formsnative-formsmd"></a>[Исходные формы](native-forms.md)

Исходные формы разрешить Xamarin.Forms [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)-производным страниц для использования в собственных проектах Xamarin.iOS, Xamarin.Android и универсальной платформы Windows (UWP).

## <a name="native-viewsnative-viewsindexmd"></a>[Исходные представления](native-views/index.md)

Исходные представления из iOS, Android и универсальной платформы Windows можно непосредственно ссылаться из Xamarin.Forms. На исходные представления можно задать свойства и обработчики событий, и они могут взаимодействовать с представления Xamarin.Forms.

## <a name="platform-specificsplatform-specificsindexmd"></a>[Особенности платформы](platform-specifics/index.md)

Особенности платформы позволяют использовать функциональные возможности, доступные только на определенной платформе, не требуя пользовательские модули подготовки отчетов или эффекты. Кроме того поставщики могут создавать свои собственные особенности платформы с эффектами.

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

В этом руководстве описывается Windows особенностей платформы Xamarin.Forms, и как добавить проект UWP в существующее решение Xamarin.Forms.
