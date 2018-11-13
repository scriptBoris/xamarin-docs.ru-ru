---
title: ViewPager с фрагментами
description: ViewPager является менеджером макета, которая позволяет реализовывать жестовую навигации. Жестовую навигации позволяет пользователю проведения пальцем влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с помощью ViewPager, с помощью фрагментов, как страницы данных.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 1b6e1c8ce91eaad46e779527c5ba12e2187cad24
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528433"
---
# <a name="viewpager-with-fragments"></a>ViewPager с фрагментами

_ViewPager является менеджером макета, которая позволяет реализовывать жестовую навигации. Жестовую навигации позволяет пользователю проведения пальцем влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с помощью ViewPager, с помощью фрагментов, как страницы данных._

 
## <a name="overview"></a>Обзор

`ViewPager` часто используется в сочетании с фрагментами, чтобы проще управлять жизненным циклом каждой страницы в `ViewPager`. В этом пошаговом руководстве `ViewPager` используется для создания приложения вызывается **FlashCardPager** , представляет собой серию математических задач на флэш-картах. Каждая карта flash реализуется как фрагмент. Пользователь предъявляет left и right через флэш-картах и касается проблемой math, чтобы отобразить ответ на него. Это приложение создает `Fragment` экземпляр для каждого адаптера на основе флэш-карт и реализует `FragmentPagerAdapter`. В [Viewpager и представления](~/android/user-interface/controls/view-pager/viewpager-and-views.md), большая часть работы, выполненной в `MainActivity` методы жизненного цикла. В **FlashCardPager**, большая часть работы будет осуществляться `Fragment` в одном из его методов жизненного цикла. 

В этом руководстве рассматриваются основы фрагментов &ndash; Если вы еще не знакомы с использованием фрагментов на Xamarin.Android, см. в разделе [фрагментов](~/android/platform/fragments/index.md) помогут вам приступить к работе с фрагментами. 



## <a name="start-an-app-project"></a>Запуск проекта приложения

Создайте новый проект Android с именем **FlashCardPager**. После этого запустите диспетчер пакетов NuGet (Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Найти и установить **Xamarin.Android.Support.v4** пакета, как описано в [Viewpager и представления](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Добавление источника данных пример

В **FlashCardPager**, источником данных является колода карт flash, представленный `FlashCardDeck` класса; эти данные источника блоки `ViewPager` с содержимым элемента. `FlashCardDeck` содержит коллекцию готовых математических задач и ответов. `FlashCardDeck` Конструктор не требует аргументов: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

Коллекция флэш-карты в `FlashCardDeck` организована таким образом, каждая карта флэш-памяти может осуществляться в индексаторе. К примеру следующий код извлекает четвертый проблема флэш-карт в колоде: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Эта строка кода получает соответствующий ответ на проблемы, описанной ранее:

```csharp
string answer = flashCardDeck[3].Answer;
```

Так как сведения о реализации `FlashCardDeck` не относящиеся к анализу `ViewPager`, `FlashCardDeck` кода нет в списке.
В исходном коде `FlashCardDeck` доступен по [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Скачайте этот исходный файл (или скопируйте и вставьте код в новый **FlashCardDeck.cs** файл) и добавьте его в проект.



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
```

Этот XML-документ определяет `ViewPager` , который занимает весь экран. Обратите внимание, что необходимо использовать полное доменное имя **android.support.v4.view.ViewPager** поскольку `ViewPager` упакован в библиотеку поддержки. `ViewPager` доступен только из [библиотеки поддержки Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); он не доступен в пакете SDK для Android.


## <a name="set-up-viewpager"></a>Настройка ViewPager

Изменить **MainActivity.cs** и добавьте следующие `using` инструкции:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Изменение `MainActivity` объявление класса, чтобы он является производным от `AppCompatActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` является производным от`FragmentActivity` (а не `Activity`) так как `FragmentActivity` знает, как управлять поддержки фрагментов. Замените метод `OnCreate` следующим кодом: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

Этот код выполняет следующие функции:

1.  Задает представление из **Main.axml** ресурс макета.

2.  Извлекает ссылку `ViewPager` из макета.

3.  Создает новый экземпляр `FlashCardDeck` как источник данных.

При построении и запустить этот код, вы увидите экран, похожий на следующем снимке экрана: 

[![Снимок экрана из FlashCardPager приложения с пустым ViewPager](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

На этом этапе `ViewPager` является пустым, так как он недостаточно фрагментов, которые используются заполнения `ViewPager`, и для создания этих фрагментов из данных в некий адаптер **FlashCardDeck**. 

В следующих разделах `FlashCardFragment` создать для реализации функциональности каждого флэш-карт и `FragmentPagerAdapter` для соединения создается `ViewPager` фрагментам, созданных на основе данных в `FlashCardDeck`. 



## <a name="create-the-fragment"></a>Создания фрагмента

Каждая карта flash будут управляться с помощью пользовательского интерфейса фрагмент, который называется `FlashCardFragment`. `FlashCardFragment`в представлении отображаются данные с одного флэш-карт. Каждый экземпляр `FlashCardFragment` будет размещено `ViewPager`. 
`FlashCardFragment`в представлении будет состоять из `TextView` , отображающий текст проблема флэш-карт. В этом представлении будут Реализуйте обработчик события, который использует `Toast` для отображения ответа в том случае, когда пользователь касается вопрос флэш-карт. 



### <a name="create-the-flashcardfragment-layout"></a>Создать макет FlashCardFragment

Прежде чем `FlashCardFragment` может быть реализован, его макет должен быть определен. Этот макет — это фрагмент контейнер для одного фрагмента. Добавить новый макет Android для **ресурсы/макета** вызывается **flashcard_layout.axml**. Откройте **Resources/layout/flashcard_layout.axml** и замените его содержимое следующим кодом: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <TextView
        android:id="@+id/flash_card_question"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:gravity="center"
            android:textAppearance="@android:style/TextAppearance.Large"
            android:textSize="100sp"
            android:layout_centerHorizontal="true"
            android:layout_centerVertical="true"
            android:text="Question goes here" />
    </RelativeLayout>
```

Этот макет определяет фрагмент единый флэш-карт; Каждый фрагмент состоит из `TextView` , отображающий ошибку шрифтом больших (100sp). Этот текст выравнивается по центру по вертикали и горизонтали на флэш-карт. 



### <a name="create-the-initial-flashcardfragment-class"></a>Создать начальный класс FlashCardFragment

Добавьте новый файл с именем **FlashCardFragment.cs** и замените его содержимое следующим кодом:

```csharp
using System;
using Android.OS;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    public class FlashCardFragment : Android.Support.V4.App.Fragment
    {
        public FlashCardFragment() { }

        public static FlashCardFragment newInstance(String question, String answer)
        {
            FlashCardFragment fragment = new FlashCardFragment();
            return fragment;
        }
        public override View OnCreateView (
            LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
        {
            View view = inflater.Inflate (Resource.Layout.flashcard_layout, container, false);
            TextView questionBox = (TextView)view.FindViewById (Resource.Id.flash_card_question);
            return view;
        }
    }
}
```

Этот код заглушает основные `Fragment` определение, которое будет использоваться для отображения флэш-карт. Обратите внимание, что `FlashCardFragment` является производным от версии библиотеки поддержки `Fragment` определенные в `Android.Support.V4.App.Fragment`. Конструктор является пустым, чтобы `newInstance` фабричный метод используется для создания нового `FlashCardFragment` вместо конструктора. 

`OnCreateView` Метод жизненного цикла, создает и настраивает `TextView`. Он увеличивает макета для фрагмента `TextView` и возвращает увеличенную `TextView` вызывающему объекту. `LayoutInflater` и `ViewGroup` передаются `OnCreateView` таким образом, он может значительно увеличить макета. `savedInstanceState` Пакета содержит данные, `OnCreateView` использует для повторного создания `TextView` из сохраненного состояния. 

Представление фрагмента явным образом за счет вызова `inflater.Inflate`. `container` Аргумент является просмотр родительского элемента и `false` флаг указывает, что процессом удержаться и не Добавление увеличенную представления для родительского представления (она будет добавлена при `ViewPager` вызов в адаптера `GetItem` метод далее в этой Пошаговое руководство по). 



### <a name="add-state-code-to-flashcardfragment"></a>Добавьте код состояния FlashCardFragment

Как и действие, фрагмент не содержит `Bundle` которого используется для сохранения и извлечения состояния. В **FlashCardPager**, этот `Bundle` используется для сохранения вопрос и ответить на текст для связанного флэш-карт. В **FlashCardFragment.cs**, добавьте следующий `Bundle` ключи в верхнюю часть `FlashCardFragment` определение класса: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Изменить `newInstance` фабричный метод, поэтому он создает `Bundle` и использует выше ключей для хранения переданного вопрос и ответить на текст в фрагменте после его создания: 

```csharp
public static FlashCardFragment newInstance(String question, String answer)
{
    FlashCardFragment fragment = new FlashCardFragment();

    Bundle args = new Bundle();
    args.PutString(FLASH_CARD_QUESTION, question);
    args.PutString(FLASH_CARD_ANSWER, answer);
    fragment.Arguments = args;

    return fragment;
}
```

Измените метод жизненного цикла фрагмент `OnCreateView` для получения этих сведений из пакета в переданном и загрузить текст вопроса в `TextBox`: 

```csharp
public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
{
    string question = Arguments.GetString(FLASH_CARD_QUESTION, "");
    string answer = Arguments.GetString(FLASH_CARD_ANSWER, "");

    View view = inflater.Inflate(Resource.Layout.flashcard_layout, container, false);
    TextView questionBox = (TextView)view.FindViewById(Resource.Id.flash_card_question);
    questionBox.Text = question;

    return view;
}
```

`answer` Переменная не используется здесь, но он будет использоваться позже при добавлении в этот файл код обработчика событий. 


## <a name="create-the-adapter"></a>Создание адаптера

`ViewPager` использует объект контроллера адаптера, которая размещается между `ViewPager` и источником данных (см. рисунок в ViewPager [адаптера](~/android/user-interface/controls/view-pager/index.md#adapter) статье). Доступ к этим данным `ViewPager` необходимо указать настраиваемый адаптер, производный от `PagerAdapter`. Поскольку в этом примере используется фрагментов, она использует `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` является производным от `PagerAdapter`. 
`FragmentPagerAdapter` Представляет каждую страницу в `Fragment` , постоянно находиться в диспетчере фрагментов для, до тех пор, пока пользователь может вернуться на страницу. Как вставляет пользователя по страницам `ViewPager`, `FragmentPagerAdapter` извлекает данные из источника данных и использует его для создания `Fragment`s для `ViewPager` для отображения. 

При реализации `FragmentPagerAdapter`, необходимо переопределить следующие:

-   **Число** &ndash; только для чтения свойство, которое возвращает количество доступных представлений (страниц).

-   **GetItem** &ndash; возвращает фрагмент для отображения для указанной страницы.

Добавьте новый файл с именем **FlashCardDeckAdapter.cs** и замените его содержимое следующим кодом:

```csharp
using System;
using Android.Views;
using Android.Widget;
using Android.Support.V4.App;

namespace FlashCardPager
{
    class FlashCardDeckAdapter : FragmentPagerAdapter
    {
        public FlashCardDeckAdapter (Android.Support.V4.App.FragmentManager fm, FlashCardDeck flashCards)
            : base(fm)
        {
        }

        public override int Count
        {
            get { throw new NotImplementedException(); }
        }

        public override Android.Support.V4.App.Fragment GetItem(int position)
        {
            throw new NotImplementedException();
        }
    }
}
```

Этот код заглушает основные `FragmentPagerAdapter` реализации. В следующих разделах каждый из этих методов заменяется рабочий код. Конструктор предназначена для передачи диспетчеру фрагмент `FlashCardDeckAdapter`в конструктор базового класса. 



### <a name="implement-the-adapter-constructor"></a>Реализуйте конструктор адаптера

Когда приложение создает экземпляр `FlashCardDeckAdapter`, он передает ссылку на диспетчер фрагментов и которого создан экземпляр `FlashCardDeck`. Добавьте следующую переменную члена в верхнюю часть `FlashCardDeckAdapter` в класс **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Добавьте следующую строку кода, чтобы `FlashCardDeckAdapter` конструктор: 

```csharp
this.flashCardDeck = flashCards;
```

Эта строка кода хранилищ `FlashCardDeck` экземпляр, на котором `FlashCardDeckAdapter` будет использовать. 



### <a name="implement-count"></a>Реализуйте Count

`Count` Реализация довольно проста: она возвращает число флэш-карты в колоде флэш-карт. Замените `Count` следующим кодом: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


`NumCards` Свойство `FlashCardDeck` возвращает количество флэш-карты (число фрагментов) в наборе данных. 



### <a name="implement-getitem"></a>Реализуйте GetItem

`GetItem` Метод возвращает фрагмент, связанные с заданной позиции. Когда `GetItem` вызывается для положения в колоде флэш-карт, она возвращает `FlashCardFragment` настроено отображение проблема флэш-карт в этой позиции. Замените метод `GetItem` следующим кодом: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Этот код выполняет следующие функции:

1.  Ищет строку проблемы math в `FlashCardDeck` колоды для заданной позиции. 

2.  Ищет строку ответов в `FlashCardDeck` колоды для заданной позиции. 

3.  Вызовы `FlashCardFragment` фабричный метод `newInstance`, передав в строках проблему и ответ флэш-карт. 

4.  Создает и возвращает новый флэш-карт `Fragment` , содержащее текст вопроса и ответа для этой позиции. 

При `ViewPager` отображает `Fragment` в `position`, он отображает `TextBox` содержащий строку проблема math, расположенный по адресу `position` в колоде флэш-карт. 



## <a name="add-the-adapter-to-the-viewpager"></a>Добавьте адаптер в ViewPager

Теперь, когда `FlashCardDeckAdapter` будет реализован, пришло время добавить его в `ViewPager`. В **MainActivity.cs**, добавьте следующую строку кода в конец `OnCreate` метод:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Этот код создает экземпляр `FlashCardDeckAdapter`, передавая `SupportFragmentManager` в первом аргументе. ( `SupportFragmentManager` Позволяет получить ссылку на свойство FragmentActivity `FragmentManager` &ndash; Дополнительные сведения о `FragmentManager`, см. в разделе [управление фрагментов](~/android/platform/fragments/managing-fragments.md).) 

Реализация ядра уже выполнено &ndash; сборка и запуск приложения.
Вы должны увидеть первое изображение колоды карт flash отображаются на экране, как показано на следующем рисунке слева. Проведите по экрану слева, чтобы увидеть дополнительные флэш-карты, затем пальцем вправо для перемещения назад по колоду карт flash:

[![Снимки экрана FlashCardPager приложение без индикаторов на пейджер](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Добавить индикатор на пейджер

Этом минимальный `ViewPager` реализации отображает каждый флэш-карт в колоде, но он обеспечивает нет сведения о том, где пользователь является в колоде. Следующим шагом является добавление `PagerTabStrip`. `PagerTabStrip` Информирует пользователя о том, какие проблемы отображается номер и предоставляет контекст навигации, отображая подсказку флэш-картах предыдущей и последующей. 

Откройте **Resources/layout/Main.axml** и добавьте `PagerTabStrip` макета:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.view.ViewPager xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/pager"
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

При построении и запуске приложения, вы должны увидеть пустую `PagerTabStrip` отображается в верхней части каждой карточки flash: 

[![Крупный план PagerTabStrip без текста](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Отображение заголовка

Чтобы добавить заголовок для каждой страницы вкладки, реализовать `GetPageTitleFormatted` метода в адаптере. `ViewPager` вызовы `GetPageTitleFormatted` (если реализовано) для получения строки заголовка, описывающий страницы в указанной позиции. Добавьте следующий метод в `FlashCardDeckAdapter` в класс **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Этот код преобразует позицию в колоду карт flash в проблему. Результирующая строка преобразуется в Java `String` , возвращаемую `ViewPager`. При запуске приложения с помощью этого нового метода, каждая страница отображает номер проблемы в `PagerTabStrip`: 

[![Снимки экрана FlashCardPager с номером проблему, отображаемый над каждой страницы](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Узнать, сколько проблему в колоду карт флэш-памяти, который отображается в верхней части каждой карточки flash считайте выполняется и обратно. 



## <a name="handle-user-input"></a>Обработки введенных пользователем данных

**FlashCardPager** представляет собой серию основе фрагмент флэш-карты в `ViewPager`, но еще не предоставляет способ отображения ответов для каждой задачи. В этом разделе добавляется обработчик событий `FlashCardFragment` для отображения ответа в том случае, когда пользователь касается по тексту, проблема флэш-карт. 

Откройте **FlashCardFragment.cs** и добавьте следующий код в конец `OnCreateView` метод непосредственно перед представления возвращается вызывающей стороне: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Это `Click` обработчик событий отображает ответ в всплывающее уведомление, которое отображается, когда пользователь касается `TextBox`. `answer` Переменная была инициализирована ранее, когда сведения о состоянии, считанный из пакета, который был передан `OnCreateView`. Построить и запустить приложение, а затем выберите текст проблему в каждой карточке flash, чтобы увидеть ответ: 

[![Снимки экрана из FlashCardPager приложение всплывающие уведомления шифрованию ошибку](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

**FlashCardPager** представленные в этом пошаговом руководстве использует `MainActivity` производным от `FragmentActivity`, но можно также создавать производные `MainActivity` из `AppCompatActivity` (которая также обеспечивает управление фрагментами). Чтобы просмотреть `AppCompatActivity` пример, см. в разделе [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/) в коллекции примеров. 



## <a name="summary"></a>Сводка

В этом пошаговом руководстве указан пошаговый пример создания базового `ViewPager`-на основе приложений с помощью `Fragment`s. Оно представлено в источник данных пример, содержащий флэш-карт вопросов и ответов, `ViewPager` макет для отображения флэш-карты и `FragmentPagerAdapter` подкласс, в котором подключается `ViewPager` к источнику данных. Чтобы помочь пользователю перемещаться по флэш-карты, инструкции были включены, объясняется, как добавить `PagerTabStrip` для отображения числа проблемы в верхней части каждой страницы. Наконец код обработки событий был добавлен для отображения ответа в том случае, когда пользователь касается над проблемой флэш-карт. 



## <a name="related-links"></a>Связанные ссылки

- [FlashCardPager (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
