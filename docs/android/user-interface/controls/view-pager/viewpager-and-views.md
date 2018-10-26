---
title: ViewPager с представлениями
description: ViewPager является менеджером макета, которая позволяет реализовывать жестовую навигации. Жестовую навигации позволяет пользователю проведения пальцем влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с помощью ViewPager с pagertabstrip, с помощью представлений, как страницы данных (последующие руководства описывается, как использовать фрагменты для страниц).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a8b7fa53d3384821d028e4a88ba22071a17e5bd9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113928"
---
# <a name="viewpager-with-views"></a>ViewPager с представлениями

_ViewPager является менеджером макета, которая позволяет реализовывать жестовую навигации. Жестовую навигации позволяет пользователю проведения пальцем влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с помощью ViewPager с pagertabstrip, с помощью представлений, как страницы данных (последующие руководства описывается, как использовать фрагменты для страниц)._

 
## <a name="overview"></a>Обзор

В этом руководстве — это пошаговое руководство, которое предоставляет пошаговую демонстрацию, как использовать `ViewPager` для реализации коллекции образов листопадное и актуальная деревьев. В этом приложении пользователь предъявляет left и right через «дерева каталог», чтобы просматривать изображения дерева. В верхней части каждой страницы каталога, имя дерева указывается в`PagerTabStrip`, и отображается изображение дерева в `ImageView`. Используется адаптер к интерфейсу `ViewPager` для базовой модели данных. Это приложение реализует производный от адаптера `PagerAdapter`. 

Несмотря на то что `ViewPager`-приложений часто реализуются с помощью `Fragment`s, существуют случаи, сравнительно простое использование где дополнительной сложности `Fragment`s не требуется. Например, приложение базового образа коллекции, в данном пошаговом руководстве не требует применения `Fragment`s. Так как содержимое является статическим, и только вставляет пользователю переключаться между различные образы, реализация может храниться проще, используя стандартные Android представления и макеты. 



## <a name="start-an-app-project"></a>Запуск проекта приложения

Создайте новый проект Android с именем **TreePager** (см. в разделе [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md) Дополнительные сведения о создании новых проектов Android). После этого запустите диспетчер пакетов NuGet. (Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Найти и установить **библиотеки поддержки Android v4**: 

[![Снимок экрана of Support Nuget версии 4, выбранные в диспетчере пакетов NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Также будут установлены reaquired любые дополнительные пакеты с **библиотеки поддержки Android v4**.



## <a name="add-an-example-data-source"></a>Добавление источника данных пример

В этом примере источник данных дерева каталога (представленный `TreeCatalog` класс) предоставляет `ViewPager` с содержимым элемента. 
`TreeCatalog` содержит коллекцию готовых образов дерева и дерево названия, адаптер будет использовать для создания `View`s. `TreeCatalog` Конструктор не требует аргументов:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

Коллекция изображений в `TreeCatalog` организована таким образом, каждое изображение может осуществляться в индексаторе. Например следующий код получает идентификатор ресурса изображения для третье изображение в коллекции: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Так как сведения о реализации `TreeCatalog` не относящиеся к анализу `ViewPager`, `TreeCatalog` кода нет в списке. В исходном коде `TreeCatalog` доступен по [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Скачайте этот исходный файл (или скопируйте и вставьте код в новый **TreeCatalog.cs** файл) и добавьте его в проект. Кроме того, скачайте и распакуйте [файлы изображений](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) в вашей **ресурсы/drawable** папки и включать их в проект. 



## <a name="create-a-viewpager-layout"></a>Создать макет ViewPager

Откройте **Resources/layout/Main.axml** и замените его содержимое следующим кодом XML:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

</android.support.v4.view.ViewPager>

```csharp
This XML defines a `ViewPager` that occupies the entire screen. Note that
you must use the fully-qualified name **android.support.v4.view.ViewPager**
because `ViewPager` is packaged in a support library. `ViewPager` is
available only from 
[Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/);
it is not available in the Android SDK. 


## Set up ViewPager

Edit **MainActivity.cs** and add the following `using` statement:

```csharp
using Android.Support.V4.View;
```

Замените метод `OnCreate` следующим кодом:

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    TreeCatalog treeCatalog = new TreeCatalog();
}
```

Этот код выполняет следующие функции:

1.  Задает представление из **Main.axml** ресурс макета.

2.  Извлекает ссылку `ViewPager` из макета.

3.  Создает новый экземпляр `TreeCatalog` как источник данных.

При построении и запустить этот код, вы увидите экран, похожий на следующем снимке экрана: 

[![Снимок экрана приложения, отображение пустой ViewPager](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

На этом этапе `ViewPager` является пустым, так как он недостаточно адаптер для доступа к содержимому в **TreeCatalog**. В следующем разделе **PagerAdapter** для соединения создается `ViewPager` для **TreeCatalog**. 


## <a name="create-the-adapter"></a>Создание адаптера

`ViewPager` использует объект контроллера адаптера, которая размещается между `ViewPager` и источником данных (см. рисунок в [адаптера](~/android/user-interface/controls/view-pager/index.md#adapter)). Чтобы получить доступ к данным, `ViewPager` необходимо указать настраиваемый адаптер, производный от `PagerAdapter`. Этот адаптер заполнения каждого `ViewPager` страницу содержимое из источника данных. Поскольку этот источник данных конкретного приложения, настраиваемый адаптер приведен код, который понимает, как доступ к данным. Как вставляет пользователя по страницам `ViewPager`, адаптер извлекает информацию из источника данных и загружает их в страницы `ViewPager` для отображения. 

При реализации `PagerAdapter`, необходимо переопределить следующие:

-   **InstantiateItem** &ndash; создает страницу (`View`) для заданной позиции и добавляет ее к `ViewPager`в коллекцию представлений. 

-   **DestroyItem** &ndash; удаляет страницу из заданной позиции.

-   **Число** &ndash; только для чтения свойство, которое возвращает количество доступных представлений (страниц). 

-   **IsViewFromObject** &ndash; определяет, связан ли страница с определенным объектом ключа. (Этот объект создается путем `InstantiateItem` метод.) В этом примере является ключевой объект `TreeCatalog` объект данных.

Добавьте новый файл с именем **TreePagerAdapter.cs** и замените его содержимое следующим кодом: 

```csharp
using System;
using Android.App;
using Android.Runtime;
using Android.Content;
using Android.Views;
using Android.Widget;
using Android.Support.V4.View;
using Java.Lang;

namespace TreePager
{
    class TreePagerAdapter : PagerAdapter
    {
        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override bool IsViewFromObject(View view, Java.Lang.Object obj)
        {
            throw new NotImplementedException();
        }

        public override Java.Lang.Object InstantiateItem (View container, int position)
        {
            throw new NotImplementedException();
        }

        public override void DestroyItem(View container, int position, Java.Lang.Object view)
        {
            throw new NotImplementedException();
        }
    }
}
```

Этот код заглушает основные `PagerAdapter` реализации. В следующих разделах каждый из этих методов заменяется рабочий код. 



### <a name="implement-the-constructor"></a>Реализуйте конструктор

Когда приложение создает экземпляр `TreePagerAdapter`, он передает контекст ( `MainActivity`) и которого создан экземпляр `TreeCatalog`. Добавьте следующие переменные-члены и конструктор в верхнюю часть `TreePagerAdapter` в класс **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

Этот конструктор предназначен для постоянного сохранения контекста и `TreeCatalog` экземпляр, на котором `TreePagerAdapter` будет использовать. 



### <a name="implement-count"></a>Реализуйте Count

`Count` Реализация довольно проста: она возвращает номер деревьев в дереве каталога. Замените `Count` следующим кодом:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`NumTrees` Свойство `TreeCatalog` возвращает число деревьев (количество страниц) в наборе данных.



### <a name="implement-instantiateitem"></a>Реализовать InstantiateItem

`InstantiateItem` Метод создает страницы для заданной позиции. Также необходимо добавить только что созданный представления `ViewPager`просмотреть коллекцию. Чтобы сделать это возможным, `ViewPager` передает себя как параметр контейнера. 

Замените метод `InstantiateItem` следующим кодом:

```csharp
public override Java.Lang.Object InstantiateItem (View container, int position)
{
    var imageView = new ImageView (context);
    imageView.SetImageResource (treeCatalog[position].imageId);
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.AddView (imageView);
    return imageView;
}
```

Этот код выполняет следующие функции:

1.  Создает новый экземпляр `ImageView` для отображения изображения дерева в указанной позиции. Приложения `MainActivity` — контекст, будут переданы `ImageView` конструктор.

2.  Наборы `ImageView` ресурса `TreeCatalog` изображения идентификатор ресурса в указанной позиции.

3.  Приводит к контейнеру переданного `View` для `ViewPager` ссылки.
    Обратите внимание, что необходимо использовать `JavaCast<ViewPager>()` должным образом провести это приведение (это необходимо, чтобы Android выполняет преобразование типов среды выполнения checked).

4.  Добавляет экземпляр `ImageView` для `ViewPager` и возвращает `ImageView` вызывающему объекту.

Когда `ViewPager` отображает изображение в `position`, он отображает это `ImageView`. Изначально `InstantiateItem` вызывается дважды, чтобы заполнить первые две страницы с представлениями. Во время прокрутки, она вызывается повторно для поддерживать представления просто программной части и будущих отображаемого элемента. 



### <a name="implement-destroyitem"></a>Реализовать DestroyItem

`DestroyItem` Метод удаляет страницу из заданной позиции. В приложениях, где можно изменить представление в любом месте `ViewPager` необходимо определить, какой метод удаления устаревших представления в этой позиции перед его замена новое представление. В `TreeCatalog` примере представление в каждой позиции не изменяется, чтобы удалить представление `DestroyItem` будет просто необходимо добавить заново при `InstantiateItem` вызывается для этой позиции. (Для повышения эффективности может внедрить повторно используемого пула `View`s, который будет повторно отображается в той же позиции.) 

Замените метод `DestroyItem` следующим кодом: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Этот код выполняет следующие функции:

1.  Приводит к контейнеру переданного `View` в `ViewPager` ссылки.

2.  Приводит переданный объект Java (`view`) в C# `View` (`view as View`);

3.  Удаляет представление из `ViewPager`. 



### <a name="implement-isviewfromobject"></a>Реализовать IsViewFromObject

Как пользователь слайдов влево и вправо по страницам содержимого `ViewPager` вызовы `IsViewFromObject` чтобы убедиться, что дочерние `View` в заданной позиции связан с объектом адаптера для этой же позиции (таким образом, называется объекта адаптера *ключа объекта*). Для относительно простых приложений ассоциации является одним из удостоверений &ndash; ключа объекта адаптера на этом экземпляре — представление, которое был возвращен `ViewPager` через `InstantiateItem`. Однако для других приложений, ключ объекта может быть некоторые другой экземпляр классу, зависящему от адаптера, который связан с (но не совпадает) дочерние представление, `ViewPager` отображает в этой позиции. Только адаптер знает, связаны ли переданный представление и ключ объекта. 

`IsViewFromObject` должен быть реализован для `PagerAdapter` функционировать должным образом. Если `IsViewFromObject` возвращает `false` для заданной позиции, `ViewPager` не будет отображаться представление в этой позиции. В `TreePager` приложение, объект ключа, возвращаемого функцией `InstantiateItem` *—* странице `View` дерева, поэтому код имеет только для проверки удостоверения (т. е., ключ объекта и представления являются одной и той же). Замените `IsViewFromObject` следующим кодом: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>Добавьте адаптер в ViewPager

Теперь, когда `TreePagerAdapter` будет реализован, пришло время добавить его в `ViewPager`. В **MainActivity.cs**, добавьте следующую строку кода в конец `OnCreate` метод:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Этот код создает экземпляр `TreePagerAdapter`, передавая `MainActivity` как контекст (`this`). Созданный экземпляр `TreeCatalog` передается второй аргумент конструктора. `ViewPager` `Adapter` Свойство имеет значение для экземпляра `TreePagerAdapter` объекта; это вилки `TreePagerAdapter` в `ViewPager`. 

Реализация ядра уже выполнено &ndash; сборка и запуск приложения. Вы должны увидеть первое изображение дерева каталога отображаются на экране, как показано на следующем рисунке слева. Проведите влево, чтобы просмотреть дополнительные представления в виде дерева, затем пальцем вправо для перемещения назад через каталог дерева: 

[![Снимки экрана из TreePager приложения считывания изображения дерева](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Добавить индикатор на пейджер

Этом минимальный `ViewPager` реализации отображаются образы дерева каталога, но он обеспечивает нет сведения о том, где пользователь является в каталоге. Следующим шагом является добавление `PagerTabStrip`. `PagerTabStrip` Информирует пользователя о какая страница отображается и предоставляет контекст навигации, отображая подсказку предыдущей и следующей страницы. `PagerTabStrip` предназначен для использования в качестве индикатора для текущей страницы `ViewPager`; он выполняет прокрутку и обновляет как вставляет пользователя на всех страницах. 

Откройте **Resources/layout/Main.axml** и добавьте `PagerTabStrip` макета:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/viewpager"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <android.support.v4.view.PagerTabStrip
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:layout_gravity="top"
          android:paddingBottom="10dp"
          android:paddingTop="10dp"
          android:textColor="#fff" />

</android.support.v4.view.ViewPager>
```

`ViewPager` и `PagerTabStrip` предназначены для совместной работы. При объявлении `PagerTabStrip` внутри `ViewPager` макета, `ViewPager` автоматически находит `PagerTabStrip` и подключить его к адаптеру. При построении и запуске приложения, вы должны увидеть пустую `PagerTabStrip` в верхней части каждого экрана: 

[![Крупный план снимок пустой PagerTabStrip](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>Отображение заголовка

Чтобы добавить заголовок для каждой страницы вкладки, реализовать `GetPageTitleFormatted` метод в `PagerAdapter`-производного класса. `ViewPager` вызовы `GetPageTitleFormatted` (если реализовано) для получения строки заголовка, описывающий страницы в указанной позиции. Добавьте следующий метод в `TreePagerAdapter` в класс **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Этот код извлекает строку заголовка дерева из указанную страницу (положение) в каталоге дерева, которая преобразуется в Java `String`и возвращает его, чтобы `ViewPager`. При запуске приложения с помощью этого нового метода для каждой страницы отображается заголовок дерева в `PagerTabStrip`. Вы должны увидеть имя дерева в верхней части экрана без подчеркивания: 

[![Снимки экрана из страниц с вкладками PagerTabStrip заполнением текста](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Можно, считайте и обратно, чтобы просмотреть каждое изображение дерева с субтитрами в каталоге. 



### <a name="pagertitlestrip-variation"></a>Вариант PagerTitleStrip

`PagerTitleStrip` очень похожа на `PagerTabStrip` за исключением того, что `PagerTabStrip` добавляет подчеркивания для выбранной вкладки. Вы можете заменить `PagerTabStrip` с `PagerTitleStrip` в макете и запустите приложение снова, чтобы увидеть, как это выглядит с выше `PagerTitleStrip`: 

[![PagerTitleStrip линией, удалены из текста](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Обратите внимание, что подчеркивание удаляется при преобразовании `PagerTitleStrip`. 


 
## <a name="summary"></a>Сводка

В этом пошаговом руководстве указан пошаговый пример создания базового `ViewPager`-на основе приложения без использования `Fragment`s. Оно представлено в источник данных пример, содержащий изображения и заголовка строки, `ViewPager` макет для отображения изображений и `PagerAdapter` подкласс, в котором подключается `ViewPager` к источнику данных. Чтобы помочь пользователю перемещаться по набору данных, инструкции были включены, объясняется, как добавить `PagerTabStrip` или `PagerTitleStrip` для отображения изображения заголовка в верхней части каждой страницы. 


## <a name="related-links"></a>Связанные ссылки

- [TreePager (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
