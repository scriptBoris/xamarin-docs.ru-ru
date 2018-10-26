---
title: Работа с tvOS элементов управления на странице в Xamarin
description: В этом документе описывается, как работать с элементами управления страницы tvOS в приложении, созданном с помощью Xamarin. Содержит общее описание элементов управления страницы, описывается, как настроить их в раскадровках и проверяет как реагировать на события изменения страницы.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 173bc7713b5b8c330d4d4c5863bef24be8bdcb52
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107473"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Работа с tvOS элементов управления на странице в Xamarin

Иногда может потребоваться отобразить ряд страниц или изображений в приложении Xamarin.tvOS. Элемент управления страницы был разработан получить четкое отображение страницы пользователь выходит на максимальное число страниц. Элемент управления страницы отображается ряд точек для темной oval форме фона. Текущая страница отобразит Закрашенная точка, все остальные страницы Показать пустыми точками. Элемент управления страницы отсекает внешнего большинство точек, если слишком много для размещения в ее область фона.

[![](page-controls-images/page01.png "Пример элемента управления страницы")](page-controls-images/page01.png#lightbox)

Элемент управления страницы в элементе Неинтерактивный режим позволяет отправить отзыв только пользователю. Необходимо будет добавить другие элементы управления, чтобы изменить номер текущей страницы (например, жестов или кнопки).

Apple имеет следующие рекомендации при использовании элемента управления страницы.

- **Используется только для полной коллекции** -элементов управления на странице лучше всего работают в среде во весь экран для отображения нескольких страниц, которые существуют в одной коллекции.
- **Ограничить число страниц** -элементов управления на странице лучше всего подходят для десяти (10) или меньше страниц и не более двадцати (20) страниц. Для более чем 20 страниц, рассмотрите возможность использования [представление коллекции](~/ios/tvos/user-interface/collection-views.md) и отображения страницы в виде сетки.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Элементы управления страницы и раскадровки

Для работы с элементами управления страницы в приложении Xamarin.tvOS проще всего добавить их в пользовательском Интерфейсе приложения с помощью конструктора iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

    
1. В **панели решения**, дважды щелкните `Main.storyboard` и откройте его для редактирования.
1. Перетащите **управления страницы** из **элементов** и поместите его в представлении: 

    [![](page-controls-images/page02.png "Элемент управления страницы")](page-controls-images/page02.png#lightbox)
1. В **вкладку графического** из **панели свойств**, вы можете настроить несколько свойств элемента управления страницы, такие как его **текущей страницы** и **# страниц**: 

    [![](page-controls-images/page03.png "На вкладке мини-приложения")](page-controls-images/page03.png#lightbox)
1. Затем добавьте элементы управления или жесты представление, чтобы переход вперед и назад по коллекции страниц.
1. Наконец, присвойте **имена** к элементам управления таким образом, может отвечать на эти файлы в C# кода. Пример: 

    [![](page-controls-images/page04.png "Имя элемента управления")](page-controls-images/page04.png#lightbox)
1. Сохраните изменения.
    

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

    
1. В **обозревателе решений**, дважды щелкните `Main.storyboard` и откройте его для редактирования.
1. Перетащите **управления страницы** из **элементов** и поместите его в представлении: 

    [![](page-controls-images/page02-vs.png "Элемент управления страницы")](page-controls-images/page02-vs.png#lightbox)
1. В **вкладку графического** из **обозревателе свойств**, вы можете настроить несколько свойств элемента управления страницы, такие как его **текущей страницы** и **# страниц**: 

    [![](page-controls-images/page03-vs.png "На вкладке мини-приложения")](page-controls-images/page03-vs.png#lightbox)
1. Затем добавьте элементы управления или жесты представление, чтобы переход вперед и назад по коллекции страниц.
1. Наконец, присвойте **имена** к элементам управления таким образом, может отвечать на эти файлы в C# кода. Пример: 

    [![](page-controls-images/page04-vs.png "Имя элемента управления")](page-controls-images/page04-vs.png#lightbox)
1. Сохраните изменения.
    

-----

> [!IMPORTANT]
> Хотя можно назначить события, такие как `TouchUpInside` элемента пользовательского интерфейса (например, UIButton) в конструкторе iOS, он никогда не вызывается так, как Apple TV нет на сенсорном экране или поддерживает события касания. Следует всегда использовать `Primary Action` событие при создании обработчиков событий для tvOS элементы пользовательского интерфейса.

Изменение контроллера представления (пример `ViewController.cs`) и добавьте код для обработки страницы изменяется. Пример:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

Давайте более подробно рассмотрим два свойства элемента управления страницы. Во-первых чтобы указать максимальное число страниц, используйте следующее:

```csharp
PageView.Pages = 6;
```

Чтобы изменить номер текущей страницы, используйте следующий код:

```csharp
PageView.CurrentPage = PageNumber;
```

`CurrentPage` Равно нулю (0), на основе, поэтому первая страница будет равно нулю, и последний будет иметь одно минус максимальное число страниц.

Дополнительные сведения о работе с раскадровками, см. в разделе наших [по Tvos краткое руководство по](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается проектирование и работать с элементом управления страницы внутри приложения Xamarin.tvOS.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
