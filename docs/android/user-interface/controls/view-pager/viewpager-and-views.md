---
title: ViewPager с представлениями
description: ViewPager представляет собой диспетчер макет, который позволяет реализовать жестовую навигации. Жестовую навигации позволяет пользователю проведите влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с ViewPager и PagerTabStrip, с помощью представлений в виде страниц данных (последующие руководстве описывается использование фрагментов для страниц).
ms.prod: xamarin
ms.assetid: 42E5379F-B0F4-4B87-A314-BF3DE405B0C8
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 39251f7cf6bc287b76b7921278853158bdb14d66
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772462"
---
# <a name="viewpager-with-views"></a>ViewPager с представлениями

_ViewPager представляет собой диспетчер макет, который позволяет реализовать жестовую навигации. Жестовую навигации позволяет пользователю проведите влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с ViewPager и PagerTabStrip, с помощью представлений в виде страниц данных (последующие руководстве описывается использование фрагментов для страниц)._

 
## <a name="overview"></a>Обзор

В этом руководстве является Пошаговое руководство, которое предоставляет пошаговую демонстрацию, как использовать `ViewPager` для реализации коллекции образов листопадное и вечнозеленое деревьев. В этом приложении пользователь предъявляет влево и вправо посредством «каталога дерева» просматривать изображения дерева. В верхней части каждой страницы каталога, имя дерева указывается в`PagerTabStrip`, а изображение дерева отображается в `ImageView`. Адаптер используется интерфейс `ViewPager` базовую модель данных. Это приложение реализует производный от адаптера `PagerAdapter`. 

Несмотря на то что `ViewPager`-приложений часто реализуются с помощью `Fragment`s, существуют некоторые варианты использования относительно простой где дополнительную сложность `Fragment`s не является обязательным. Например, приложение базового образа коллекции, в данном пошаговом руководстве не требуется использовать `Fragment`s. Поскольку содержимое является статическим, и только вставляет пользователя обратно на различные изображения, реализации могут храниться проще с помощью стандартных Android представления и макеты. 



## <a name="start-an-app-project"></a>Запуск проекта приложения

Создайте новый проект Android с именем **TreePager** (см. [Привет, Android](~/android/get-started/hello-android/hello-android-quickstart.md) Дополнительные сведения о создании новых проектов Android). После этого запустите диспетчер пакетов NuGet. (Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Найти и установить **библиотеку поддержки Android версии 4**: 

[![Снимок экрана поддержки v4 Nuget, выбранные в диспетчере пакетов NuGet](viewpager-and-views-images/01-install-support-lib-sml.png)](viewpager-and-views-images/01-install-support-lib.png#lightbox)

Также будут установлены все reaquired дополнительных пакетов с **библиотеку поддержки Android версии 4**.



## <a name="add-an-example-data-source"></a>Добавить источник данных примера

В этом примере источник данных каталога дерева (представленного `TreeCatalog` класс) предоставляет `ViewPager` с содержимым элемента. 
`TreeCatalog` содержит коллекцию готовых образов дерева и заголовки дерева, которые адаптер будет использовать для создания `View`s. `TreeCatalog` Конструктор не требует аргументов:

```csharp
TreeCatalog treeCatalog = new TreeCatalog();
```

Коллекция изображений в `TreeCatalog` организована таким образом, что каждое изображение может осуществляться индексатора. Например следующий код извлекает идентификатор ресурса изображения для третье изображение в коллекции: 

```csharp
int imageId = treeCatalog[2].imageId;
```

Так как сведения о реализации `TreeCatalog` не являются значимыми для понимания `ViewPager`, `TreeCatalog` кода нет в списке. Исходный код для `TreeCatalog` доступен на [TreeCatalog.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/TreePager/TreeCatalog.cs). Загрузить этот исходный файл (или скопируйте и вставьте код в новый **TreeCatalog.cs** файла) и добавьте его в проект. Кроме того, загрузите и распакуйте [файлы изображений](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/TreePager/Resources/tree-images.zip?raw=true) в ваш **ресурсы или drawable** папки и включить их в проект. 



## <a name="create-a-viewpager-layout"></a>Создание макета ViewPager

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

Этот код выполняет следующие задачи.

1.  Задает представление из **Main.axml** ресурсов макета.

2.  Извлекает ссылку на `ViewPager` от макета.

3.  Создает новый `TreeCatalog` как источник данных.

При создании и запуске этого кода, вы увидите, что отображаемое похожа на следующем снимке экрана: 

[![Снимок экрана: Отображение пустой ViewPager приложения](viewpager-and-views-images/02-initial-screen-sml.png)](viewpager-and-views-images/02-initial-screen.png#lightbox)

На этом этапе `ViewPager` пуст, так как он недостаточно для доступа к содержимому в адаптер **TreeCatalog**. В следующем разделе **PagerAdapter** создается для подключения `ViewPager` для **TreeCatalog**. 


## <a name="create-the-adapter"></a>Создание адаптера

`ViewPager` использует объект контроллера адаптера, который располагается между `ViewPager` и источником данных (показано на рисунке в [адаптер](~/android/user-interface/controls/view-pager/index.md#adapter)). Чтобы получить доступ к данным, `ViewPager` необходимо указать пользовательский адаптер, производный от `PagerAdapter`. Этот адаптер заполнения каждого `ViewPager` страницы с содержимым источника данных. Поскольку этот источник данных зависит от конкретного приложения, настраиваемого адаптера является код, способный доступа к данным. Как вставляет пользователя по страницам `ViewPager`, адаптер извлекает данные из источника данных и загружает их в страницы для `ViewPager` для отображения. 

При реализации `PagerAdapter`, необходимо переопределить следующие:

-   **InstantiateItem** &ndash; создает страницу (`View`) для заданной позиции и добавляет его в `ViewPager`в коллекцию представлений. 

-   **DestroyItem** &ndash; удаляет страницу из заданной позиции.

-   **Число** &ndash; только для чтения свойство, которое возвращает количество доступных представлений (страниц). 

-   **IsViewFromObject** &ndash; определяет, является ли страница связан с определенным объектом ключа. (Этот объект создается путем `InstantiateItem` метод.) В этом примере — ключевой объект `TreeCatalog` объект данных.

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

Этот код заглушает основные `PagerAdapter` реализации. В следующих разделах каждый из этих методов заменяется рабочего кода. 



### <a name="implement-the-constructor"></a>Реализуйте конструктор

Когда приложение создает экземпляр `TreePagerAdapter`, то он передает контекст ( `MainActivity`) и экземпляр `TreeCatalog`. Добавьте следующие переменные-члены и конструктор в верхней части `TreePagerAdapter` класса в **TreePagerAdapter.cs**: 

```csharp
Context context;
TreeCatalog treeCatalog;

public TreePagerAdapter (Context context, TreeCatalog treeCatalog)
{
    this.context = context;
    this.treeCatalog = treeCatalog;
}
```

Этот конструктор предназначен для хранения контекста и `TreeCatalog` экземпляр `TreePagerAdapter` будет использовать. 



### <a name="implement-count"></a>Реализуйте Count

`Count` Реализацию относительно проста: он возвращает число деревьев в каталоге дерева. Замените `Count` следующим кодом:

```csharp
public override int Count
{
    get { return treeCatalog.NumTrees; }
}
```

`NumTrees` Свойство `TreeCatalog` возвращает число деревьев (количество страниц) в наборе данных.



### <a name="implement-instantiateitem"></a>Реализуйте InstantiateItem

`InstantiateItem` Метод создает страницу для заданной позиции. Также необходимо добавить новые представления `ViewPager`просмотра коллекции. Для этого, `ViewPager` передает себя в качестве параметра контейнера. 

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

Этот код выполняет следующие задачи.

1.  Создает новый `ImageView` для отображения изображения дерева в указанной позиции. Приложения `MainActivity` контекст, который будет передан `ImageView` конструктор.

2.  Наборы `ImageView` ресурс `TreeCatalog` изображения идентификатор ресурса в указанной позиции.

3.  Приводит переданный контейнера `View` для `ViewPager` ссылки.
    Обратите внимание, что необходимо использовать `JavaCast<ViewPager>()` для выполнения такого приведения (это необходимо, чтобы Android выполняет преобразование типов среды выполнения проверки) правильно.

4.  Добавляет экземпляр `ImageView` для `ViewPager` и возвращает `ImageView` вызывающему объекту.

Когда `ViewPager` отображает изображение в `position`, он отображает это `ImageView`. Изначально `InstantiateItem` вызывается дважды, чтобы заполнить первые две страницы с представлениями. При переходе пользователя, он вызывается повторно и поддерживать представления только за и будущих отображаемого элемента. 



### <a name="implement-destroyitem"></a>Реализуйте DestroyItem

`DestroyItem` Метод удаляет страницу из заданной позиции. В приложениях, где можно изменить представление в любом месте `ViewPager` должен иметь какой-либо способ удаления устаревших представления в этой позиции перед его замена новое представление. В `TreeCatalog` примере представление в каждой позиции не изменяется, чтобы удалить представление `DestroyItem` будет повторно добавлены при `InstantiateItem` вызывается в этой позиции. (Для повышения эффективности может реализовать пула, который нужно перезапустить `View`s, повторно отображается в том же месте.) 

Замените метод `DestroyItem` следующим кодом: 

```csharp
public override void DestroyItem(View container, int position, Java.Lang.Object view)
{
    var viewPager = container.JavaCast<ViewPager>();
    viewPager.RemoveView(view as View);
}
```

Этот код выполняет следующие задачи.

1.  Приводит переданный контейнера `View` в `ViewPager` ссылки.

2.  Приводит переданный объект Java (`view`) в C# `View` (`view as View`);

3.  Удаляет представление из `ViewPager`. 



### <a name="implement-isviewfromobject"></a>Реализуйте IsViewFromObject

Как пользователь слайдов влево и вправо по страницам содержимое `ViewPager` вызовы `IsViewFromObject` и убедитесь, что дочерние `View` в заданной позиции связан с объектом адаптера для этой же позиции (таким образом, вызывается объекта адаптера *ключа объекта*). Для относительно простых приложений связь является одним из удостоверения &ndash; ключа объекта адаптера на этом экземпляре — представление, которое было ранее возвращено `ViewPager` через `InstantiateItem`. Однако для других приложений, ключ объекта может быть некоторого класса адаптера экземпляра, связанный с (но не идентичны) дочерние представление, `ViewPager` отображается в этой позиции. Только адаптер знает, связаны ли переданный представление и ключ объекта. 

`IsViewFromObject` должен быть реализован для `PagerAdapter` функционировать должным образом. Если `IsViewFromObject` возвращает `false` для заданной позиции, `ViewPager` представлении не отображаются в этой позиции. В `TreePager` приложение, объект ключа, возвращенных `InstantiateItem` *—* страницы `View` дерева, поэтому код должен только для проверки удостоверения (т. е., ключ объекта и представления одной и той же). Замените `IsViewFromObject` следующим кодом: 

```csharp
public override bool IsViewFromObject(View view, Java.Lang.Object obj)
{
    return view == obj;
}
```


## <a name="add-the-adapter-to-the-viewpager"></a>Добавление адаптера ViewPager

Теперь, когда `TreePagerAdapter` будет реализован, пришло время, чтобы добавить его в `ViewPager`. В **MainActivity.cs**, добавьте следующую строку кода в конец `OnCreate` метод:

```csharp
viewPager.Adapter = new TreePagerAdapter(this, treeCatalog);
```

Этот код создает экземпляр `TreePagerAdapter`, передавая `MainActivity` как контекст (`this`). Созданный экземпляр `TreeCatalog` передается в конструктор второй аргумент. `ViewPager` `Adapter` Задано для экземпляра `TreePagerAdapter` объекта; этот вилки `TreePagerAdapter` в `ViewPager`. 

Реализация основных компонентов завершена &ndash; построение и запуск приложения. Вы увидите первое изображение каталога дерева отображается на экране, как показано на следующем снимке экрана слева. Проведите влево, чтобы увидеть дополнительные представления в виде дерева, затем проведите пальцем вправо для перемещения назад по дерева каталога: 

[![Снимки экрана TreePager приложения считывания через дерево изображений](viewpager-and-views-images/03-example-views-sml.png)](viewpager-and-views-images/03-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Добавить индикатор страничного навигатора

Этот минимальный `ViewPager` реализацию отображает изображения дерева каталога, но он не указываются относительно где пользователь входит в каталоге. Следующим шагом является добавление `PagerTabStrip`. `PagerTabStrip` Сообщает пользователю о страницы, на которую отображается и предоставляет контекст навигации, отображая подсказку предыдущей и следующей страницы. `PagerTabStrip` предназначен для использования в качестве индикатора для текущей страницы `ViewPager`; он выполняет прокрутку и обновляет как вставляет пользователя на всех страницах. 

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

`ViewPager` и `PagerTabStrip` предназначены для совместной работы. При объявлении `PagerTabStrip` внутри `ViewPager` макета, `ViewPager` автоматически находит `PagerTabStrip` и подключите его к адаптеру. При построении и запуске приложения вы увидите пустые `PagerTabStrip` в верхней части каждого экрана: 

[![Снимок экрана Крупный план пустой PagerTabStrip](viewpager-and-views-images/04-empty-pagetabstrip-cap-sml.png)](viewpager-and-views-images/04-empty-pagetabstrip-cap.png#lightbox)



### <a name="display-a-title"></a>Отображать заголовок

Чтобы добавить заголовок для каждой вкладки, реализовать `GetPageTitleFormatted` метод в `PagerAdapter`-производного класса. `ViewPager` вызовы `GetPageTitleFormatted` (Если реализована) для получения строки заголовка, описывающий страницы в указанной позиции. Добавьте следующий метод `TreePagerAdapter` класса в **TreePagerAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String(treeCatalog[position].caption);
}
```

Этот код извлекает строку заголовка дерева из указанной странице (позиция) в каталоге дерева, преобразует его в Java `String`и возвращает его в `ViewPager`. При запуске приложения с помощью этого нового метода каждой страницы отображается заголовок дерева в `PagerTabStrip`. Вы увидите имя дерева в верхней части экрана без подчеркивания: 

[![Снимки экрана для страницы с вкладками PagerTabStrip заполнения текста](viewpager-and-views-images/05-final-pagetabstrip-sml.png)](viewpager-and-views-images/05-final-pagetabstrip.png#lightbox)

Можно проведите назад и вперед для просмотра изображений с надписями дерева в каталоге. 



### <a name="pagertitlestrip-variation"></a>Вариант PagerTitleStrip

`PagerTitleStrip` очень похоже на `PagerTabStrip` за исключением того, что `PagerTabStrip` добавляет подчеркивания для выбранной вкладки. Можно заменить `PagerTabStrip` с `PagerTitleStrip` в указанных выше макета и запустить приложение еще раз, чтобы увидеть, как он отображается с `PagerTitleStrip`: 

[![PagerTitleStrip линией, удалены из текста](viewpager-and-views-images/06-pagetitlestrip-example-sml.png)](viewpager-and-views-images/06-pagetitlestrip-example.png#lightbox)

Обратите внимание, что подчеркивание удаляется при преобразовании `PagerTitleStrip`. 


 
## <a name="summary"></a>Сводка

В этом пошаговом руководстве предоставленный пример с пошаговыми инструкциями по построению базового `ViewPager`-основаны приложения без использования `Fragment`s. Оно представлено в источник данных примера, содержащий изображений и строк заголовка `ViewPager` макет для отображения изображений и `PagerAdapter` подкласс, в котором подключается `ViewPager` к источнику данных. Чтобы помочь пользователю перемещаться по набор данных, входившие в инструкции, объясняется, как добавить `PagerTabStrip` или `PagerTitleStrip` для отображения изображения заголовка в верхней части каждой страницы. 


## <a name="related-links"></a>Связанные ссылки

- [TreePager (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/TreePager)
