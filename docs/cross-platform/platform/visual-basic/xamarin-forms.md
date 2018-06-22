---
title: С помощью Visual Basic.NET Xamarin.Forms
description: Шаблон проекта Xamarin.Forms PCL можно изменить для использования Visual Basic для главной сборки, фактически, позволяя создавать кросс платформенных мобильных приложений с помощью VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: b858e26de95d2abbc23917b1ed5a1de65105cd8d
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2018
ms.locfileid: "33917867"
---
# <a name="xamarinforms-using-visual-basicnet"></a>С помощью Visual Basic.NET Xamarin.Forms

Xamarin непосредственно не поддерживает Visual Basic — следуйте инструкциям на эту страницу, чтобы создать решение Xamarin.Forms PCL C#, а затем заменить общий проект PCL кода Visual Basic.

[![](xamarin-forms-images/hero-sml.png "Создание решения Xamarin.Forms PCL и замените общий проект PCL кода Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Visual Studio в Windows необходимо использовать программу с помощью Visual Basic.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin.Forms с помощью Visual Basic пошагового руководства

Выполните следующие действия для создания простого проекта Xamarin.Forms с помощью Visual Basic.

1. Создайте новый *Xamarin.Forms C#* решения, использующего переносимой библиотеки классов (PCL).
Последовательно выберите пункты **файл > Новый проект** и в **новый проект** окна перейдите к **установленные > Шаблоны > Visual C# > кроссплатформенный** выберите  **Межплатформенные приложения платформы (Xamarin.Forms или машинный код) > Xamarin.Forms**.

2. Щелкните правой кнопкой мыши решение и **Добавить > Новый проект**.

3. Выберите **Visual Basic > Библиотека классов (переносимая)** тип проекта:

   [![](xamarin-forms-images/add-vb-2-sml.png "Добавление нового проекта переносимой библиотеки классов")](xamarin-forms-images/add-vb-2.png#lightbox)

4. Выберите платформы, как показано для настройки профиля PCL (не забудьте добавить Xamarin.iOS и Xamarin.Android):

   ![](xamarin-forms-images/add-vb-3-sml.png "Выберите платформы, для поддержки")

5. Правой кнопкой мыши проект Visual Basic и выберите **свойства**, затем измените **пространство имен по умолчанию** для сопоставления существующих C# проектов:

   ![](xamarin-forms-images/add-vb-4s-sml.png "Убедитесь, что приложение Xamarin.Forms соответствует корневое пространство имен Visual Basic")

6. Щелкните правой кнопкой мыши новый проект Visual Basic и выберите **управление пакетами Nuget**, затем установите **Xamarin.Forms** и закрыть окно диспетчера пакетов.

   [![](xamarin-forms-images/add-vb-4-sml.png "Формы и закрыть окно диспетчера пакетов")](xamarin-forms-images/add-vb-4.png#lightbox)

7. По умолчанию **Class1** файл *и* класса `App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "Переименуйте файл Class1 по умолчанию и класс в приложение")](xamarin-forms-images/add-vb-5.png#lightbox)

8. Вставьте следующий код в **App.vb** файл, который станет начальной точки приложения Xamarin.Forms. Не забудьте включить `Imports Xamarin.Forms` и добавьте `Inherits Application` в класс:

    ```vb 
    Imports Xamarin.Forms

    Public Class App
        Inherits Application

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Welcome to Xamarin.Forms with Visual Basic.NET"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Dim page = New ContentPage
            page.Content = stack
            MainPage = page

        End Sub

    End Class
    ```

9. Теперь необходимо указывать проекты iOS и Android на новый проект Visual Basic.
Щелкните правой кнопкой мыши **ссылки** узел в проекты iOS и Android для открытия **диспетчер ссылок**. Отменяет деления переносимой библиотеки C# и делений переносимой библиотеки VB (не забыть, это можно сделать для проектов Android и iOS).

   [![](xamarin-forms-images/add-vb-8-sml.png "Удалить старые ссылку на проект, добавить Справочник по языку Visual Basic")](xamarin-forms-images/add-vb-8.png#lightbox)

10. Удалите переносимого проекта C#. Добавить новые **.vb** приложения Xamarin.Forms выходных файлов для сборки. Шаблон для новых `ContentPage`s в Visual Basic, показано ниже:

    ```vb
    Imports Xamarin.Forms

    Public Class Page2
    Inherits ContentPage

        Public Sub New()
            Dim label = New Label With {.HoriztonalTextAlignment = TextAlignment.Center,
                                        .FontSize = Device.GetNamedSize(NamedSize.Medium, GetType(Label)),
                                        .Text = "Visual Basic ContentPage"}

            Dim stack = New StackLayout With {
                .VerticalOptions = LayoutOptions.Center
            }
            stack.Children.Add(label)

            Content = stack
        End Sub
    End Class
    ```

## <a name="limitations-of-visual-basic-in-xamarinforms"></a>Ограничения Visual Basic в Xamarin.Forms

Как указано на [страницы переносимой Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md), Xamarin не поддерживает язык Visual Basic. Это означает, что существуют некоторые ограничения на использования Visual Basic:

 - Пользовательские модули подготовки отчетов не может быть написан на Visual Basic, они должны быть написан на языке C# проекты собственной платформы.

 - Реализации служб зависимостей нельзя записать в Visual Basic, они должен быть написан на языке C# проекты собственной платформы.

 - XAML-страницы не может быть включен в проект Visual Basic — генератор кода может создавать только C#. Существует возможность включить XAML в отдельный, на которую указывает ссылка, переносимой библиотеки классов C# и использовать привязку данных для заполнения XAML-файлов через модель Visual Basic (примером включается в [пример](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

 - Xamarin поддерживает язык Visual Basic.NET.

## <a name="related-links"></a>Связанные ссылки

- [XamarinFormsVB (пример)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Межплатформенная разработка в .NET Framework (Майкрософт)](http://msdn.microsoft.com/en-us/library/gg597391(v=vs.110).aspx)
