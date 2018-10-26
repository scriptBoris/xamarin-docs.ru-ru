---
title: Работа с tvOS текста и полей поиска в Xamarin
description: В этом документе описывается работа с полями текста и поиска в приложение tvOS, созданных с помощью Xamarin. Он представлен общий обзор полей текста и поиска и обсуждаются клавиатуры, интеграции раскадровки, моделей данных поиска и многое другое.
ms.prod: xamarin
ms.assetid: 9EE63CA6-2F31-4EE0-AAE5-82E18CFAC06C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: f1085044f2147bec0910a87f8a6174c4648ef9b8
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120155"
---
# <a name="working-with-tvos-text-and-search-fields-in-xamarin"></a>Работа с tvOS текста и полей поиска в Xamarin

При необходимости, Xamarin.tvOS приложение может запрашивать небольших фрагментов текста от пользователя (например, идентификаторы пользователей и пароли) с помощью текстового поля и экранной клавиатуры:

[![](text-fields-and-search-images/intro01.png "Образец поля поиска")](text-fields-and-search-images/intro01.png#lightbox)

При необходимости можно указать ключевое слово возможности поиска содержимого приложения, с помощью поля поиска:

[![](text-fields-and-search-images/intro02.png "Пример результатов поиска")](text-fields-and-search-images/intro02.png#lightbox)

В этом документе рассматриваются сведения о работе с текстом и полей в приложении Xamarin.tvOS.

<a name="About-Text-and-Search-Fields" />

## <a name="about-text-and-search-fields"></a>О текста и полей поиска

Как уже говорилось выше, при необходимости, ваш Xamarin.tvOS может представлять один или несколько текстовых полей для сбора небольших объемов текста с помощью пользователя на экране (или клавиатуры bluetooth необязательно в зависимости от версии пользователь установил tvOS). 

Кроме того Если ваше приложение представляет больших объемов содержимого для пользователя (например, музыка, фильмы или коллекции изображений), может потребоваться включить поле поиска, которое позволяет пользователю ввести небольшой объем текст для фильтрации списка доступных элементов.

<a name="Text-Fields" />

## <a name="text-fields"></a>Текстовые поля

В tvOS, текстовое поле, представляется в виде фиксированной высоты, округленное углу запись рамка, приведет к появлению экранную клавиатуру, когда пользователь щелкает его:

[![](text-fields-and-search-images/text01.png "Текст поля в tvOS")](text-fields-and-search-images/text01.png#lightbox)

Когда пользователь перемещает [фокус](~/ios/tvos/app-fundamentals/navigation-focus.md) для заданного текстового поля, он будет больше и отображать глубокого тень. Необходимо это помнить при проектировании пользовательского интерфейса, как текстовые поля могут перекрывать друг друга других элементов пользовательского интерфейса, когда фокус.

Apple имеет следующие рекомендации по работе с текстовыми полями:

- **Текст записи только в случае необходимости использовать** — из-за природы экранную клавиатуру, введя много разделов текста или заполнении несколько текстовых полей — утомительно для пользователя. Лучше ограничить объем ввода текста с помощью списков выбора или [кнопки](~/ios/tvos/user-interface/buttons.md).
- **Использование подсказок для обмена данными цели** -текстовое поле может отображать заполнитель «подсказки», если значение пусто. Там, где это применимо, используйте ссылки, чтобы описать назначение текстовое поле вместо отдельных метки.
- **Выберите соответствующий тип клавиатуры по умолчанию** -tvOS предоставляет несколько разных типов, специально созданная клавиатуры, которые можно указать для текстового поля. Например клавиатура адрес электронной почты может упростить запись, позволяя пользователю выбрать из списка недавно введенные адреса.
- **Когда это необходимо, используйте поля Secure** -Secure текстовое поле представляет знаки при вводе точки (вместо реальной буквы). Всегда используйте Secure текстовое поле при сборе конфиденциальные сведения, например пароли.

<a name="Keyboards" />

## <a name="keyboards"></a>Клавиатуры

Всякий раз, когда пользователь щелкает поле текст в пользовательском интерфейсе, линейный на экране отображается клавиатуры. Пользователь использует области Touch [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) для выбора отдельных букв с помощью клавиатуры и введите требуемые сведения:

[![](text-fields-and-search-images/keyboard01.png "Siri Remote клавиатуры")](text-fields-and-search-images/keyboard01.png#lightbox)

Если имеется более одного текстового поля в текущем представлении, **Далее** автоматически будет отображаться кнопка для перевода пользователя к следующему полю текста. Объект **сделать** кнопка будет отображаться для последнего текстового поля, который будет завершить ввод текста и возвращает пользователя к предыдущему экрану. 

В любое время, пользователь может также нажать **меню** кнопки на удаленном Siri, чтобы завершить ввод текста и снова вернуться к предыдущему экрану.

Apple имеет следующие рекомендации по работе с экранной клавиатуры:

- **Выберите соответствующий тип клавиатуры по умолчанию** -tvOS предоставляет несколько разных типов, специально созданная клавиатуры, которые можно указать для текстового поля. Например клавиатура адрес электронной почты может упростить запись, позволяя пользователю выбрать из списка недавно введенные адреса.
- **Когда это необходимо, используйте представления периферийного устройства клавиатуры** — в дополнение к стандартной информацию, которая всегда отображается, необязательно представления периферийного устройства (например, изображения или метки), которые могут добавляться к экранной клавиатуры объяснить ввод текста или к помочь пользователю ввести необходимые сведения.

Дополнительные сведения о работе с экранной клавиатуры, см. в разделе Apple [UIKeyboardType](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextInputTraits_Protocol/index.html#//apple_ref/c/tdef/UIKeyboardType), [управление клавиатуры](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW1), [настраиваемые представления для входных данных](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/InputViews/InputViews.html#//apple_ref/doc/uid/TP40009542-CH12-SW1) и [ Текст руководство по программированию для iOS](https://developer.apple.com/library/tvos/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/Introduction/Introduction.html) документации.

<a name="Search" />

## <a name="search"></a>Поиск

Поле поиска представления специализированные экрана, предоставляя текстовое поле и экранную клавиатуру, пользователь может фильтровать коллекцию элементов, отображенных под клавиатуры:

[![](text-fields-and-search-images/search01.png "Пример результатов поиска")](text-fields-and-search-images/search01.png#lightbox)

Как пользователь введет буквы в поле поиска, результаты ниже будет автоматически отражать результаты поиска. В любое время пользователь может переместить фокус результаты и выберите один из элементов, представленных.

Apple имеет следующие рекомендации по работе с помощью поля поиска.

- **Укажите последних поисков** — так как ввода текста с удаленной Siri может занять много времени, и Пользователи склонны повторить поисковые запросы, рассмотрите возможность добавления раздела последних результатов поиска перед текущие результаты в области клавиатуры.
- **По возможности ограничить число результатов** — так как большой список элементов может быть трудно пользователю синтаксический анализ и перехода, рекомендуется ограничить число возвращаемых результатов.
- **При необходимости укажите результат поиска фильтрует** — Если содержимое, предоставляемое приложением поддается, рассмотрите возможность добавления область панели, чтобы разрешить пользователю для дальнейшей фильтрации возвращаемых результатах поиска.

Дополнительные сведения см. в разделе Apple [ссылки на класс UISearchController](https://developer.apple.com/library/tvos/documentation/UIKit/Reference/UISearchController/index.html).

<a name="Working-with-Text-Fields" />

## <a name="working-with-text-fields"></a>Работа с текстовыми полями

Для работы с текстовыми полями в приложении Xamarin.tvOS проще всего добавить их в проект интерфейса пользователя, с помощью конструктора iOS.

Выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. В **панели решения**, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования.
1. Перетащите один или несколько **текстовые поля** int в область конструктора на представления: 

    [![](text-fields-and-search-images/text02.png "Текстовое поле")](text-fields-and-search-images/text02.png#lightbox)
1. Выберите **текстовые поля** и дать каждому уникальный **имя** в **мини-приложение** вкладке **панели свойств**: 

    [![](text-fields-and-search-images/text03.png "На вкладке \"мини-приложение\" в панели свойств")](text-fields-and-search-images/text03.png#lightbox)
1. В **текстовое поле** можно определить элементы, такие как **заполнитель** подсказки и по умолчанию **значение**: 

    [![](text-fields-and-search-images/text04.png "В разделе текстовое поле")](text-fields-and-search-images/text04.png#lightbox)
1. Прокрутите вниз, чтобы определить свойства, например **проверка орфографии**, **регистр букв** и значение по умолчанию **тип клавиатуры**: 

    [![](text-fields-and-search-images/text05.png "Проверка орфографии, регистр букв и тип клавиатуры по умолчанию")](text-fields-and-search-images/text05.png#lightbox) 
1. Сохраните изменения для раскадровки.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. В **обозревателе решений** дважды щелкните файл `Main.storyboard`, чтобы открыть его для редактирования.
1. Перетащите один или несколько **текстовые поля** int в область конструктора на представления: 

    [![](text-fields-and-search-images/text02-vs.png "Текстовое поле")](text-fields-and-search-images/text02-vs.png#lightbox)
1. Выберите **текстовые поля** и дать каждому уникальный **имя** в **мини-приложение** вкладке **обозревателе свойств**: 

    [![](text-fields-and-search-images/text03-vs.png "На вкладке мини-приложения")](text-fields-and-search-images/text03-vs.png#lightbox)
1. В **текстовое поле** можно определить элементы, такие как **заполнитель** подсказки и по умолчанию **значение**: 

    [![](text-fields-and-search-images/text04-vs.png "В разделе текстовое поле")](text-fields-and-search-images/text04-vs.png#lightbox)
1. Прокрутите вниз, чтобы определить свойства, например **проверка орфографии**, **регистр букв** и значение по умолчанию **тип клавиатуры**: 

    [![](text-fields-and-search-images/text05-vs.png "Проверка орфографии, регистр букв и тип клавиатуры по умолчанию")](text-fields-and-search-images/text05-vs.png#lightbox) 
1. Сохраните изменения для раскадровки.
    
-----

В коде, вы можете получить или задать значение текстового поля с помощью его `Text` свойство:

```csharp
Console.WriteLine ("User ID {0} and Password {1}", UserId.Text, Password.Text);
```

При необходимости можно использовать `Started` и `Ended` текстовое поле события реагировать на ввод текста начала и окончания.

<a name="Working-with-Search-Fields" />

## <a name="working-with-search-fields"></a>Работа с полями поиска

Чтобы работать с полем поиска в приложении Xamarin.tvOS проще всего добавить их в проект интерфейса пользователя, с помощью конструктора интерфейса.

Выполните следующие действия:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)
    
1. В **панели решения**, дважды щелкните `Main.storyboard` файл, чтобы открыть его для редактирования.
1. Перетащите новый контроллер представления коллекций в раскадровку для представления результатов поиска пользователя: 

    [![](text-fields-and-search-images/search02.png "Контроллер представления коллекции")](text-fields-and-search-images/search02.png#lightbox)
1. В **мини-приложение** вкладке **панели свойств**, использовать `SearchResultsViewController` для **класс** и `SearchResults` для **идентификатора раскадровки**: 

    [![](text-fields-and-search-images/search03.png "На вкладке мини-приложения")](text-fields-and-search-images/search03.png#lightbox)
1. Выберите **прототип ячейки** в рабочей области конструирования.
1. В **мини-приложение** вкладке **обозревателе свойств**, использовать `SearchResultCell` для **класс** и `ImageCell` для **идентификатор**: 

    [![](text-fields-and-search-images/search04.png "На вкладке мини-приложения")](text-fields-and-search-images/search04.png#lightbox)
1. Макет конструктора из **прототип ячейки** и предоставлять каждого элемента с уникальным **имя** в **мини-приложение** вкладке **обозревателе свойств**: 

    [![](text-fields-and-search-images/search05.png "Макет разработки прототипа ячейки")](text-fields-and-search-images/search05.png#lightbox)
1. Сохраните изменения для раскадровки.
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
1. В **обозревателе решений** дважды щелкните файл `Main.storyboard`, чтобы открыть его для редактирования.
1. Перетащите новый контроллер представления коллекций в раскадровку для представления результатов поиска пользователя: 

    [![](text-fields-and-search-images/seach02-vs.png "Контроллер представления коллекции")](text-fields-and-search-images/seach02-vs.png#lightbox)
1. В **мини-приложение** вкладке **обозревателе свойств**, использовать `SearchResultsViewController` для **класс** и `SearchResults` для **идентификатора раскадровки**: 

    [![](text-fields-and-search-images/search03-vs.png "На вкладке мини-приложения")](text-fields-and-search-images/search03-vs.png#lightbox)
1. Выберите **прототип ячейки** в рабочей области конструирования.
1. В **мини-приложение** вкладке **обозревателе свойств**, использовать `SearchResultCell` для **класс** и `ImageCell` для **идентификатор**: 

    [![](text-fields-and-search-images/search04-vs.png "На вкладке мини-приложения")](text-fields-and-search-images/search04-vs.png#lightbox)
1. Макет конструктора из **прототип ячейки** и предоставлять каждого элемента с уникальным **имя** в **мини-приложение** вкладке **обозревателе свойств**: 

    [![](text-fields-and-search-images/search05-vs.png "Макет разработки прототипа ячейки")](text-fields-and-search-images/search05-vs.png#lightbox)
1. Сохраните изменения для раскадровки.
    
-----

<a name="Provide-a-Data-Model" />

### <a name="provide-a-data-model"></a>Разработать модель данных

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

Затем необходимо будет предоставить класс в качестве модели данных для результатов, пользователь будет выполняться поиск. В **обозревателе решений**, щелкните правой кнопкой мыши имя проекта и выберите **добавить** > **новый файл...**   >  **Общие** > **пустой класс** и предоставить **имя**: 

[![](text-fields-and-search-images/search06.png "Выберите пустой класс и укажите имя")](text-fields-and-search-images/search06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Затем необходимо будет предоставить класс в качестве модели данных для результатов, пользователь будет выполняться поиск. В **обозревателе решений**, щелкните правой кнопкой мыши имя проекта и выберите **добавить** > **новый элемент...**   >  **Apple** > **Прочее** > **класс** и предоставить **имя**: 

[![](text-fields-and-search-images/search06-vs.png "Выберите класс и укажите имя")](text-fields-and-search-images/search06-vs.png#lightbox)

-----

Например приложение, которое позволяет пользователю для поиска в коллекции изображений, Title и ключевое слово может выглядеть следующим образом:

```csharp
using System;
using Foundation;

namespace tvText
{
    public class PictureInformation : NSObject
    {
        #region Computed Properties
        public string Title { get; set;}
        public string ImageName { get; set;}
        public string Keywords { get; set;}
        #endregion

        #region Constructors
        public PictureInformation (string title, string imageName, string keywords)
        {
            // Initialize
            this.Title = title;
            this.ImageName = imageName;
            this.Keywords = keywords;
        }
        #endregion
    }
}
```

<a name="The-Collection-View-Cell" />

### <a name="the-collection-view-cell"></a>Ячейка представления коллекций

С моделью данных в месте, изменить **ячейки прототип** (`SearchResultViewCell.cs`) и сделать его внешний вид лежат следующие:

```csharp
using Foundation;
using System;
using UIKit;

namespace tvText
{
    public partial class SearchResultViewCell : UICollectionViewCell
    {
        #region Private Variables
        private PictureInformation _pictureInfo = null;
        #endregion

        #region Computed Properties
        public PictureInformation PictureInfo {
            get { return _pictureInfo; }
            set {
                _pictureInfo = value;
                UpdateUI ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultViewCell (IntPtr handle) : base (handle)
        {
            // Initialize
            UpdateUI ();
        }
        #endregion

        #region Private Methods
        private void UpdateUI ()
        {
            // Anything to process?
            if (PictureInfo == null) return;

            try {
                Picture.Image = UIImage.FromBundle (PictureInfo.ImageName);
                Picture.AdjustsImageWhenAncestorFocused = true;
                Title.Text = PictureInfo.Title;
                TextColor = UIColor.LightGray;
            } catch {
                // Ignore errors if view isn't fully loaded
            }
        }
        #endregion
    }

}
```

`UpdateUI` Метод будет использоваться для отображения отдельных полей из **PictureInformation** элементы ( `PictureInfo` свойство) в именованные элементы пользовательского интерфейса при каждом обновлении свойства. Например изображение и заголовок, связанный с изображением.

<a name="The-Collection-View-Controller" />

### <a name="the-collection-view-controller"></a>Контроллер представления коллекций

Далее следует изменить контроллера представления коллекции результатов поиска (`SearchResultsViewController.cs`) и это должно выглядеть следующим образом:

```csharp
using Foundation;
using System;
using UIKit;
using System.Collections.Generic;

namespace tvText
{
    public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
    {
        #region Constants
        public const string CellID = "ImageCell";
        #endregion

        #region Private Variables
        private string _searchFilter = "";
        #endregion

        #region Computed Properties
        public List<PictureInformation> AllPictures { get; set;}
        public List<PictureInformation> FoundPictures { get; set; }
        public string SearchFilter {
            get { return _searchFilter; }
            set {
                _searchFilter = value.ToLower();
                FindPictures ();
                CollectionView?.ReloadData ();
            }
        }
        #endregion

        #region Constructors
        public SearchResultsViewController (IntPtr handle) : base (handle)
        {
            // Initialize
            this.AllPictures = new List<PictureInformation> ();
            this.FoundPictures = new List<PictureInformation> ();
            PopulatePictures ();
            FindPictures ();

        }
        #endregion

        #region Private Methods
        private void PopulatePictures ()
        {
            // Clear list
            AllPictures.Clear ();

            // Add images
            AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
            AllPictures.Add (new PictureInformation ("Cheese Plate", "CheesePlate", "cheese,plate,bread"));
            AllPictures.Add (new PictureInformation ("Coffee House", "CoffeeHouse", "coffee,people,menu,restaurant,cafe"));
            AllPictures.Add (new PictureInformation ("Computer and Expresso", "ComputerExpresso", "computer,coffee,expresso,phone,notebook"));
            AllPictures.Add (new PictureInformation ("Hamburger", "Hamburger", "meat,bread,cheese,tomato,pickle,lettus"));
            AllPictures.Add (new PictureInformation ("Lasagna Dinner", "Lasagna", "salad,bread,plate,lasagna,pasta"));
            AllPictures.Add (new PictureInformation ("Expresso Meeting", "PeopleExpresso", "people,bag,phone,expresso,coffee,table,tablet,notebook"));
            AllPictures.Add (new PictureInformation ("Soup and Sandwich", "SoupAndSandwich", "soup,sandwich,bread,meat,plate,tomato,lettus,egg"));
            AllPictures.Add (new PictureInformation ("Morning Coffee", "TabletCoffee", "tablet,person,man,coffee,magazine,table"));
            AllPictures.Add (new PictureInformation ("Evening Coffee", "TabletMagCoffee", "tablet,magazine,coffee,table"));
        }

        private void FindPictures ()
        {
            // Clear list
            FoundPictures.Clear ();

            // Scan each picture for a match
            foreach (PictureInformation picture in AllPictures) {
                if (SearchFilter == "") {
                    // If no search term, everything matches
                    FoundPictures.Add (picture);
                } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
                    // If the search term is in the title or keywords, we've found a match
                    FoundPictures.Add (picture);
                }
            }
        }
        #endregion

        #region Override Methods
        public override nint NumberOfSections (UICollectionView collectionView)
        {
            // Only one section in this collection
            return 1;
        }

        public override nint GetItemsCount (UICollectionView collectionView, nint section)
        {
            // Return the number of matching pictures
            return FoundPictures.Count;
        }

        public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // Get a new cell and return it
            var cell = collectionView.DequeueReusableCell (CellID, indexPath);
            return (UICollectionViewCell)cell;
        }

        public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
        {
            // Grab the cell
            var currentCell = cell as SearchResultViewCell;
            if (currentCell == null)
                throw new Exception ("Expected to display a `SearchResultViewCell`.");

            // Display the current picture info in the cell
            var item = FoundPictures [indexPath.Row];
            currentCell.PictureInfo = item;
        }

        public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
        {
            // If this Search Controller was presented as a modal view, close
            // it before continuing
            // DismissViewController (true, null);

            // Grab the picture being selected and report it
            var picture = FoundPictures [indexPath.Row];
            Console.WriteLine ("Selected: {0}", picture.Title);
        }

        public void UpdateSearchResultsForSearchController (UISearchController searchController)
        {
            // Save the search filter and update the Collection View
            SearchFilter = searchController.SearchBar.Text ?? string.Empty;
        }

        public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
        {
            var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
            if (previousItem != null) {
                UIView.Animate (0.2, () => {
                    previousItem.TextColor = UIColor.LightGray;
                });
            }

            var nextItem = context.NextFocusedView as SearchResultViewCell;
            if (nextItem != null) {
                UIView.Animate (0.2, () => {
                    nextItem.TextColor = UIColor.Black;
                });
            }
        }
        #endregion
    }
}
```

Во-первых, `IUISearchResultsUpdating` интерфейс будет добавлен в класс для обработки фильтр контроллера поиска, обновляются пользователем:

```csharp
public partial class SearchResultsViewController : UICollectionViewController , IUISearchResultsUpdating
```

Константа также определяется, чтобы указать идентификатор **ячейки прототип** (, соответствующий идентификатор, определенный в конструкторе интерфейса выше), будет использоваться позже при контроллера коллекции запрашивает новую ячейку:

```csharp
public const string CellID = "ImageCell";
```

Полный список элементов, искомых условие фильтра поиска создается хранилище и список элементов, соответствующих этого термина:

```csharp
private string _searchFilter = "";
...

public List<PictureInformation> AllPictures { get; set;}
public List<PictureInformation> FoundPictures { get; set; }
public string SearchFilter {
    get { return _searchFilter; }
    set {
        _searchFilter = value.ToLower();
        FindPictures ();
        CollectionView?.ReloadData ();
    }
}
```

Когда `SearchFilter` будет изменен, обновляется список соответствующих элементов и загружается содержимое представления коллекции. `FindPictures` Процедура отвечает за поиск нужных элементов, которые соответствуют новой поисковому запросу:

```csharp
private void FindPictures ()
{
    // Clear list
    FoundPictures.Clear ();

    // Scan each picture for a match
    foreach (PictureInformation picture in AllPictures) {
        if (SearchFilter == "") {
            // If no search term, everything matches
            FoundPictures.Add (picture);
        } else if (picture.Title.Contains (SearchFilter) || picture.Keywords.Contains (SearchFilter)) {
            // If the search term is in the title or keywords, we've found a match
            FoundPictures.Add (picture);
        }
    }
}
```

Значение `SearchFilter` будет обновляться (которая обновляет представление коллекции результатов) когда пользователь изменяет фильтр в контроллере поиска:

```csharp
public void UpdateSearchResultsForSearchController (UISearchController searchController)
{
    // Save the search filter and update the Collection View
    SearchFilter = searchController.SearchBar.Text ?? string.Empty;
}
```

`PopulatePictures` Метод изначально заполняет коллекцию доступных элементов:

```csharp
private void PopulatePictures ()
{
    // Clear list
    AllPictures.Clear ();

    // Add images
    AllPictures.Add (new PictureInformation ("Antipasta Platter","Antipasta","cheese,grapes,tomato,coffee,meat,plate"));
    ...
}
```

Для данного примера все образцы данных создается в памяти при загрузке контроллера представления коллекций. В реальном приложении эти данные, скорее всего, будут считываться из базы данных или веб-службы, и только при необходимости, чтобы предотвратить переполнение большом Apple недостаточно памяти.

`NumberOfSections` И `GetItemsCount` методы предоставляют количество соответствующих элементов:

```csharp
public override nint NumberOfSections (UICollectionView collectionView)
{
    // Only one section in this collection
    return 1;
}

public override nint GetItemsCount (UICollectionView collectionView, nint section)
{
    // Return the number of matching pictures
    return FoundPictures.Count;
}
```

`GetCell` Метод возвращает новый **ячейки прототип** (на основе `CellID` определенный выше в раскадровке) для каждого элемента в представлении коллекции:

```csharp
public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
{
    // Get a new cell and return it
    var cell = collectionView.DequeueReusableCell (CellID, indexPath);
    return (UICollectionViewCell)cell;
}
```

`WillDisplayCell` Метод вызывается до ячейки, отображаемого, поэтому его можно настроить:

```csharp
public override void WillDisplayCell (UICollectionView collectionView, UICollectionViewCell cell, NSIndexPath indexPath)
{
    // Grab the cell
    var currentCell = cell as SearchResultViewCell;
    if (currentCell == null)
        throw new Exception ("Expected to display a `SearchResultViewCell`.");

    // Display the current picture info in the cell
    var item = FoundPictures [indexPath.Row];
    currentCell.PictureInfo = item;
}
```

`DidUpdateFocus` Метод предоставляет визуальную обратную связь пользователю, как они выделять элементы в представлении коллекции результатов:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    var previousItem = context.PreviouslyFocusedView as SearchResultViewCell;
    if (previousItem != null) {
        UIView.Animate (0.2, () => {
            previousItem.TextColor = UIColor.LightGray;
        });
    }

    var nextItem = context.NextFocusedView as SearchResultViewCell;
    if (nextItem != null) {
        UIView.Animate (0.2, () => {
            nextItem.TextColor = UIColor.Black;
        });
    }
}
```

Наконец `ItemSelected` метод обрабатывает пользователя, выбирающего элемент (щелкнув на поверхности Touch с удаленной Siri) в представлении коллекции результатов:

```csharp
public override void ItemSelected (UICollectionView collectionView, NSIndexPath indexPath)
{
    // If this Search Controller was presented as a modal view, close
    // it before continuing
    // DismissViewController (true, null);

    // Grab the picture being selected and report it
    var picture = FoundPictures [indexPath.Row];
    Console.WriteLine ("Selected: {0}", picture.Title);
}
```

Если поля поиска был представлен как модальное диалоговое окно представления (поверх представления, вызов этого метода), используйте `DismissViewController` метод, чтобы закрыть представление поиска, когда пользователь выбирает элемент. В этом примере поля поиска представляется в виде содержимое вкладки представление вкладки, чтобы оно не было отклонено здесь.

Дополнительные сведения о представлениях коллекций, см. в разделе наших [работа с представлениями коллекции](~/ios/tvos/user-interface/collection-views.md) документации.

<a name="Presenting the Search Field" />

### <a name="presenting-the-search-field"></a>Представления поля поиска

Существует два основных способа, поля поиска (и связанный с ним экранную клавиатуру и результаты поиска) могут быть представлены пользователю в tvOS: 

- **Модальное диалоговое окно представления** -поля поиска могут быть представлены через текущего представления и контроллера представления в виде модального диалогового окна во весь экран. Обычно это делается в ответ на щелчок кнопки или другого элемента пользовательского интерфейса. Диалоговое окно закрывается, когда пользователь выбирает элемент в результатах поиска.
- **Просмотр содержимого** -поле поиска является непосредственной частью данного представления. Например как содержимое вкладки поиска в контроллер представления вкладки.

Например список изображений, приведенных выше поля поиска как просмотреть содержимое на вкладке "Поиск" и контроллера представления вкладки поиска выглядит следующим образом:

```csharp
using System;
using UIKit;

namespace tvText
{
    public partial class SecondViewController : UIViewController
    {
        #region Constants
        public const string SearchResultsID = "SearchResults";
        #endregion

        #region Computed Properties
        public SearchResultsViewController ResultsController { get; set;}
        #endregion

        #region Constructors
        public SecondViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Private Methods
        public void ShowSearchController ()
        {
            // Build an instance of the Search Results View Controller from the Storyboard
            ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
            if (ResultsController == null)
                throw new Exception ("Unable to instantiate a SearchResultsViewController.");

            // Create an initialize a new search controller
            var searchController = new UISearchController (ResultsController) {
                SearchResultsUpdater = ResultsController,
                HidesNavigationBarDuringPresentation = false
            };

            // Set any required search parameters
            searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

            // The Search Results View Controller can be presented as a modal view
            // PresentViewController (searchController, true, null);

            // Or in the case of this sample, the Search View Controller is being
            // presented as the contents of the Search Tab directly. Use either one
            // or the other method to display the Search Controller (not both).
            var container = new UISearchContainerViewController (searchController);
            var navController = new UINavigationController (container);
            AddChildViewController (navController);
            View.Add (navController.View);
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // If the Search Controller is being displayed as the content
            // of the search tab, include it here.
            ShowSearchController ();
        }

        public override void ViewDidAppear (bool animated)
        {
            base.ViewDidAppear (animated);

            // If the Search Controller is being presented as a modal view,
            // call it here to display it over the contents of the Search
            // tab.
            // ShowSearchController ();
        }
        #endregion
    }
}
```

Во-первых, определяется константой, соответствующий **идентификатор раскадровки** , назначенный для контроллера представления коллекции результатов поиска в конструкторе интерфейса:

```csharp
public const string SearchResultsID = "SearchResults";
```

Далее, `ShowSearchController` метод создает новый контроллер представления коллекции поиска и отображает его требовалось:

```csharp
public void ShowSearchController ()
{
    // Build an instance of the Search Results View Controller from the Storyboard
    ResultsController = Storyboard.InstantiateViewController (SearchResultsID) as SearchResultsViewController;
    if (ResultsController == null)
        throw new Exception ("Unable to instantiate a SearchResultsViewController.");

    // Create an initialize a new search controller
    var searchController = new UISearchController (ResultsController) {
        SearchResultsUpdater = ResultsController,
        HidesNavigationBarDuringPresentation = false
    };

    // Set any required search parameters
    searchController.SearchBar.Placeholder = "Enter keyword (e.g. coffee)";

    // The Search Results View Controller can be presented as a modal view
    // PresentViewController (searchController, true, null);

    // Or in the case of this sample, the Search View Controller is being
    // presented as the contents of the Search Tab directly. Use either one
    // or the other method to display the Search Controller (not both).
    var container = new UISearchContainerViewController (searchController);
    var navController = new UINavigationController (container);
    AddChildViewController (navController);
    View.Add (navController.View);
}
```

В приведенный выше метод, один раз `SearchResultsViewController` был создан экземпляр из раскадровки, новый `UISearchController` создается для представления поля поиска и экранную клавиатуру для пользователя. Коллекция результатов поиска (согласно определению `SearchResultsViewController`) будет отображаться под этой клавиатуры.

Далее, `SearchBar` конфигурируется с информацией о, такие как **заполнитель** подсказку. Это дает сведения о тип выполняемого поиска в том случае.

Поле поиска является отображаться для пользователя в одном из двух способов:

- **Модальное диалоговое окно представления** - `PresentViewController` был вызван для представления поиска через существующее представление, весь экран.
- **Просмотр содержимого** - `UISearchContainerViewController` создается в контроллер поиска. Объект `UINavigationController` создается в контейнере поиска, то контроллер навигации добавляется на контроллер представления `AddChildViewController (navController)`и представлением `View.Add (navController.View)`.

Наконец и еще раз на основе представления типа, либо `ViewDidLoad` или `ViewDidAppear` вызовет метод `ShowSearchController` способ предоставления поиск пользователя:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // If the Search Controller is being displayed as the content
    // of the search tab, include it here.
    ShowSearchController ();
}

public override void ViewDidAppear (bool animated)
{
    base.ViewDidAppear (animated);

    // If the Search Controller is being presented as a modal view,
    // call it here to display it over the contents of the Search
    // tab.
    // ShowSearchController ();
}
```

При запуске приложения и вкладки поиска, выбранный пользователем, нефильтрованные полный список элементов, он будет отображен пользователю:

[![](text-fields-and-search-images/intro02.png "Результаты поиска по умолчанию")](text-fields-and-search-images/intro02.png#lightbox)

Как пользователь начинает ввод условия поиска, список результатов отобранные этот термин и обновляется автоматически.

[![](text-fields-and-search-images/intro03.png "Отфильтрованные результаты")](text-fields-and-search-images/intro03.png#lightbox)

В любое время пользователь может переключить фокус на элемент в результатах поиска и щелкните область Touch удаленного Siri, чтобы выбрать его.

<a name="Summary" />

## <a name="summary"></a>Сводка

В этой статье подробно рассматривается проектирование и работать с текстом и полей внутри приложения Xamarin.tvOS. Он показал, как создать содержимое текста и поиска коллекции в конструкторе интерфейса, и он показал, в поле поиска можно представить пользователю в tvOS два различных способа.



## <a name="related-links"></a>Связанные ссылки

- [Примеры tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [Человека направляющие интерфейса tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Приложение руководство по программированию для tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
