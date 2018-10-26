---
title: Работа с tvOS панели навигации в Xamarin
description: В этом документе описывается, как работать с панели навигации в приложении tvOS, созданных с помощью Xamarin. В нем описывается настройка панели навигации в раскадровке и реагирование на события из этих кнопок.
ms.prod: xamarin
ms.assetid: 74E396B7-87F0-46F7-BC6C-827DB8884C97
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 81e6cfe1e532bcfa7616e35adb28b314587bafc8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106953"
---
# <a name="working-with-tvos-navigation-bars-in-xamarin"></a>Работа с tvOS панели навигации в Xamarin

Панели навигации можно добавить в верхнюю часть представления, чтобы отобразить заголовок и необязательно кнопок панели навигации. Обычно они используются, когда пользователь переходит с главной страницы, как таблицы представления, коллекции или чтобы вложенное представление, с подробными сведениями выбранного элемента меню.

[![](navigation-bars-images/navbar01.png "Пример панели навигации")](navigation-bars-images/navbar01.png#lightbox)

В дополнение к заголовку (которое отображается в центре) панелей навигации может содержать один или несколько кнопок панели навигации (`UIBarButtonItem`) на левой и правой стороны панели.

> [!IMPORTANT]
> Панели переходов полностью прозрачны по умолчанию. Чтобы обеспечить содержимое панели навигации для чтения содержимого под ним следует соблюдать осторожность. Например, когда в представлении таблицы или коллекции прокрутка содержимого под ним.

<a name="Navigation-Bars-and-Storyboards" />

## <a name="navigation-bars-and-storyboards"></a>Панели навигации и раскадровки

Для работы с панели навигации в приложении Xamarin.tvOS проще всего добавить их в пользовательском Интерфейсе приложения с помощью конструктора iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. В **панели решения**, дважды щелкните `Main.storyboard` и откройте его для редактирования.
1. Перетащите **панель навигации** из **элементов** и поместите его на представление в верхней части экрана: 

    [![](navigation-bars-images/navbar02.png "Панель навигации")](navigation-bars-images/navbar02.png#lightbox)
1. Дважды щелкните **панель навигации** выбора **элемент навигации**. В **мини-приложение** вкладке **панели свойств**, можно задать **Title**: 

    [![](navigation-bars-images/navbar03.png "Задание заголовка")](navigation-bars-images/navbar03.png#lightbox)
1. Затем можно добавить один или несколько **элементы панели кнопку** на любом конце панели: 

    [![](navigation-bars-images/navbar04.png "На линейчатой элемент кнопки")](navigation-bars-images/navbar04.png#lightbox)
1. Наконец, доступ к сети **элементы панели кнопку** на действия в **события** вкладке **обозревателе свойств**: 

    [![](navigation-bars-images/navbar05.png "На линейчатой действие элемента кнопки")](navigation-bars-images/navbar05.png#lightbox)
1. Сохраните изменения.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. В **обозревателе решений**, дважды щелкните `Main.storyboard` и откройте его для редактирования.
1. Перетащите **панель навигации** из **элементов** и поместите его на представление в верхней части экрана: 

    [![](navigation-bars-images/navbar02-vs.png "Панель навигации")](navigation-bars-images/navbar02-vs.png#lightbox)
1. Дважды щелкните **панель навигации** выбора **элемент навигации**. В **мини-приложение** вкладке **обозревателе свойств**, можно задать **Title**: 

    [![](navigation-bars-images/navbar03-vs.png "Задание заголовка")](navigation-bars-images/navbar03-vs.png#lightbox)
1. Затем можно добавить один или несколько **элементы панели кнопку** на любом конце панели: 

    [![](navigation-bars-images/navbar04-vs.png "На линейчатой кнопку элементов")](navigation-bars-images/navbar04-vs.png#lightbox)
1. Наконец, доступ к сети **элементы панели кнопку** на действия в **события** вкладке **обозревателе свойств**: 

    [![](navigation-bars-images/navbar05-vs.png "На линейчатой действия элемента кнопок")](navigation-bars-images/navbar05-vs.png#lightbox)
1. Сохраните изменения.


-----

> [!IMPORTANT]
> Хотя можно назначить события, такие как `TouchUpInside` элемента пользовательского интерфейса (например, UIButton) в конструкторе iOS, он никогда не вызывается так, как Apple TV нет на сенсорном экране или поддерживает события касания. Следует всегда использовать `Primary Action` событие при создании обработчиков событий для tvOS элементы пользовательского интерфейса.

Ниже приведен пример обработчики событий на трех разных BarButtonItems: `ShowFirstHotel`, `ShowSecondHotel`, и `ShowThirdHotel`. При выборе каждого элемента фоновое изображение `HotelImage` изменяется. Он настраивается в контроллер представления (пример `ViewController.cs`) файл:

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();
            // Perform any additional setup after loading the view, typically from a nib.
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void ShowFirstHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel01.jpg");
        }

        partial void ShowSecondHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel02.jpg");
        }

        partial void ShowThirdHotel (UIBarButtonItem sender) {
            // Change background image
            HotelImage.Image = UIImage.FromFile("Motel03.jpg");
        }
        #endregion
    }
}
```

Пока кнопки `Enabled` свойство `true` и он не рассматривается другим элементом управления или представлении, его можно сделать элемент с фокусом, с помощью удаленного Siri.

Дополнительные сведения о работе с раскадровками, см. в разделе наших [по Tvos краткое руководство по](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается проектирование и работать с панели переходов внутри приложения Xamarin.tvOS.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
