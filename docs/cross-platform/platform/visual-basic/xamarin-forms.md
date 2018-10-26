---
title: Xamarin.Forms с помощью Visual Basic.NET
description: Шаблон проекта переносимой библиотеки Классов Xamarin.Forms можно изменить для использования Visual Basic для главной сборки, фактически, позволяя создавать кросс платформенные мобильные приложения с использованием VB.NET.
ms.prod: xamarin
ms.assetid: da4b4ba9-9205-47dc-8bae-23272ede2c50
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: f397cf595a9ae151c5f105341733b2c57023fe99
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50109722"
---
# <a name="xamarinforms-using-visual-basicnet"></a>Xamarin.Forms с помощью Visual Basic.NET

Xamarin непосредственно не поддерживает Visual Basic — следуйте инструкциям на этой странице, чтобы создать решение Xamarin.Forms PCL C#, а затем заменить общий проект переносимой библиотеки Классов кода Visual Basic.

[![](xamarin-forms-images/hero-sml.png "Создание решения Xamarin.Forms переносимой библиотеки Классов, а затем заменить общий проект переносимой библиотеки Классов кода Visual Basic")](xamarin-forms-images/hero.png#lightbox)

> [!NOTE]
> Программирование с использованием Visual Basic, необходимо использовать Visual Studio на Windows.

## <a name="xamarinforms-with-visual-basic-walkthrough"></a>Xamarin.Forms с помощью пошагового руководства Visual Basic

Выполните следующие действия для создания простого проекта Xamarin.Forms с помощью Visual Basic.

1. Создайте новый *Xamarin.Forms C#* решение, использующее переносимых библиотек классов (PCL).
Перейдите к **файл > Новый проект** и в **новый проект** окне перейдите к **установленные > Шаблоны > Visual C# > кросс-платформенные** выберите **Cross платформы App (Xamarin.Forms или машинный код) > Xamarin.Forms**.

2. Щелкните правой кнопкой мыши решение и **Добавить > Новый проект**.

3. Выберите **Visual Basic > Библиотека классов (переносимая)** тип проекта:

   [![](xamarin-forms-images/add-vb-2-sml.png "Добавление нового проекта переносимой библиотеки классов")](xamarin-forms-images/add-vb-2.png#lightbox)

4. Выберите платформы, как показано для настройки правильный профиль PCL (не забудьте включить Xamarin.iOS и Xamarin.Android):

   ![](xamarin-forms-images/add-vb-3-sml.png "Выберите платформы, для поддержки")

5. Правой кнопкой мыши проект Visual Basic и выберите **свойства**, затем измените **пространство имен по умолчанию** в соответствии с существующей C# проектов:

   ![](xamarin-forms-images/add-vb-4s-sml.png "Убедитесь, что приложение Xamarin.Forms соответствует корневое пространство имен Visual Basic")

6. Правой кнопкой мыши новый проект Visual Basic и выберите **управление пакетами Nuget**, затем установите **Xamarin.Forms** и закройте окно диспетчера пакетов.

   [![](xamarin-forms-images/add-vb-4-sml.png "Формы и закрыть окно диспетчера пакетов")](xamarin-forms-images/add-vb-4.png#lightbox)

7. Измените имя по умолчанию **Class1** файл *и* класс `App`:

   [![](xamarin-forms-images/add-vb-5-sml.png "Переименуйте файл Class1 по умолчанию и класс в приложение")](xamarin-forms-images/add-vb-5.png#lightbox)

8. Вставьте следующий код в **App.vb** файл, который станет начальной точки приложение Xamarin.Forms. Не забудьте включить `Imports Xamarin.Forms` и добавьте `Inherits Application` к классу:

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

9. Теперь нужно, чтобы они указывали на новый проект Visual Basic проекты iOS и Android.
Щелкните правой кнопкой мыши **ссылки** узел в проекты iOS и Android для открытия **диспетчер ссылок**. Отменить делений C# переносимой библиотеки и делений переносимой библиотеки VB (не забудьте, сделать это для проектов Android и iOS).

   [![](xamarin-forms-images/add-vb-8-sml.png "Удалить старые ссылки на проект, добавьте Справочник по языку Visual Basic")](xamarin-forms-images/add-vb-8.png#lightbox)

10. Удалите переносимого проекта C#. Добавить новый **.vb** файлов для построения out приложения Xamarin.Forms. Шаблон для новых `ContentPage`s в Visual Basic, показано ниже:

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

Как уже говорилось в [страницы переносимой Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md), Xamarin не поддерживает язык Visual Basic. Это означает, что существуют некоторые ограничения, на которых можно использовать Visual Basic:

 - Пользовательские модули подготовки отчетов не может записываться в Visual Basic, они должны создаваться на C# в проекты собственной платформы.

 - Зависимость реализаций службы не могут записываться в Visual Basic, они должны создаваться на C# в проекты собственной платформы.

 - Страницы XAML не может использоваться в проекте Visual Basic — только можно создать генератор кода C#. Существует возможность включить XAML в отдельном, на которую указывает ссылка, переносимой библиотеки классов C# и использовать привязки данных для заполнения файлов XAML с использованием моделей Visual Basic (пример этого включается в [пример](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB/XamlPages)).

 - Xamarin не поддерживает язык Visual Basic.NET.

## <a name="related-links"></a>Связанные ссылки

- [XamarinFormsVB (пример)](https://github.com/xamarin/mobile-samples/tree/master/VisualBasic/XamarinFormsVB)
- [Кроссплатформенная разработка в .NET Framework](https://docs.microsoft.com/dotnet/standard/cross-platform/)
