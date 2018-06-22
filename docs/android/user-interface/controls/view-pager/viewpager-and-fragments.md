---
title: ViewPager с помощью фрагментов
description: ViewPager представляет собой диспетчер макет, который позволяет реализовать жестовую навигации. Жестовую навигации позволяет пользователю проведите влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с ViewPager, с помощью фрагментов в виде страниц данных.
ms.prod: xamarin
ms.assetid: 62B6286F-3680-48F3-B91B-453692E457E5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/01/2018
ms.openlocfilehash: 83b9123b24b39e2d7bda392d05c6595f5fc3af0d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30772420"
---
# <a name="viewpager-with-fragments"></a>ViewPager с помощью фрагментов

_ViewPager представляет собой диспетчер макет, который позволяет реализовать жестовую навигации. Жестовую навигации позволяет пользователю проведите влево и вправо для пошагового просмотра страниц данных. В этом руководстве объясняется, как реализовать swipeable пользовательского интерфейса с ViewPager, с помощью фрагментов в виде страниц данных._

 
## <a name="overview"></a>Обзор

`ViewPager` часто используется в сочетании с фрагментами, чтобы оно было проще управлять жизненным циклом каждой страницы в `ViewPager`. В этом пошаговом руководстве `ViewPager` используется для создания приложения вызывается **FlashCardPager** , представляет собой серию арифметические задачи на флэш-карты. Каждая карта flash реализуется в виде фрагмента. Пользователь предъявляет влево и вправо до флэш-карты и нажимает на проблему математические, чтобы отобразить ответ на него. Это приложение создает `Fragment` экземпляра для каждого адаптера на основе флэш-карт и реализует `FragmentPagerAdapter`. В [Viewpager и представления](~/android/user-interface/controls/view-pager/viewpager-and-views.md), большая часть работы, выполненной в `MainActivity` методов жизненного цикла. В **FlashCardPager**, большая часть работы будет осуществляться `Fragment` в одном из этих методов жизненного цикла. 

В данном руководстве не рассматривается основы фрагментов &ndash; см. Если вы еще не знакомы с использованием фрагментов на Xamarin.Android, [фрагментов](~/android/platform/fragments/index.md) помогут вам приступить к работе с фрагментами. 



## <a name="start-an-app-project"></a>Запуск проекта приложения

Создайте новый проект Android с именем **FlashCardPager**. После этого запустите диспетчер пакетов NuGet (Дополнительные сведения об установке пакетов NuGet см. в разделе [Пошаговое руководство: включая NuGet в проекте](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough)). Найти и установить **Xamarin.Android.Support.v4** пакета, как описано в статье [Viewpager и представления](~/android/user-interface/controls/view-pager/viewpager-and-views.md). 



## <a name="add-an-example-data-source"></a>Добавить источник данных примера

В **FlashCardPager**, источником данных является карт флэш-памяти, представленного `FlashCardDeck` класса; эти данные источника питания `ViewPager` с содержимым элемента. `FlashCardDeck` содержит коллекцию готовых арифметические задачи и ответов. `FlashCardDeck` Конструктор не требует аргументов: 

```csharp
FlashCardDeck flashCards = new FlashCardDeck();
```

Коллекция карт флэш-памяти в `FlashCardDeck` организована таким образом, что каждая карта флэш-памяти может осуществляться индексатора. Например следующий код извлекает четвертый проблема флэш-карт в колоде в: 

```csharp
string problem = flashCardDeck[3].Problem;
```

Эта строка кода получает соответствующий ответ на предыдущей ошибка:

```csharp
string answer = flashCardDeck[3].Answer;
```

Так как сведения о реализации `FlashCardDeck` не являются значимыми для понимания `ViewPager`, `FlashCardDeck` кода нет в списке.
Исходный код для `FlashCardDeck` доступен на [FlashCardDeck.cs](https://github.com/xamarin/monodroid-samples/blob/master/UserInterface/FlashCardPager/FlashCardPager/FlashCardDeck.cs).
Загрузить этот исходный файл (или скопируйте и вставьте код в новый **FlashCardDeck.cs** файла) и добавьте его в проект.



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
```

Этот XML-код определяет `ViewPager` , занимает весь экран. Обратите внимание, что необходимо использовать полное имя **android.support.v4.view.ViewPager** из-за `ViewPager` упаковывается в библиотеке технической поддержки. `ViewPager` доступен только из [библиотеку поддержки Android версии 4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/); он недоступен в Android SDK.


## <a name="set-up-viewpager"></a>Настройка ViewPager

Изменить **MainActivity.cs** и добавьте следующее `using` инструкции:

```csharp
using Android.Support.V4.View;
using Android.Support.V4.App;
```

Изменение `MainActivity` объявления класса, чтобы он является производным от `AppCompatActivity`:

```csharp
public class MainActivity : FragmentActivity
```

`MainActivity` является производным от`FragmentActivity` (а не `Activity`) из-за `FragmentActivity` знает, как управлять поддержки фрагментов. Замените метод `OnCreate` следующим кодом: 

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);
    SetContentView(Resource.Layout.Main);
    ViewPager viewPager = FindViewById<ViewPager>(Resource.Id.viewpager);
    FlashCardDeck flashCards = new FlashCardDeck();
}
```

Этот код выполняет следующие задачи.

1.  Задает представление из **Main.axml** ресурсов макета.

2.  Извлекает ссылку на `ViewPager` от макета.

3.  Создает новый `FlashCardDeck` как источник данных.

При создании и запуске этого кода, вы увидите, что отображаемое похожа на следующем снимке экрана: 

[![Снимок экрана FlashCardPager приложения с пустым ViewPager](viewpager-and-fragments-images/01-initial-screen-sml.png)](viewpager-and-fragments-images/01-initial-screen.png#lightbox)

На этом этапе `ViewPager` является пустым, так как он хватает фрагментов, которые используются заполнения `ViewPager`, и отсутствует адаптер для создания этих фрагментов данных из **FlashCardDeck**. 

В следующих разделах `FlashCardFragment` создают для реализации функциональности каждой карты флэш-памяти и `FragmentPagerAdapter` создается для подключения `ViewPager` на фрагменты, созданные на основе данных в `FlashCardDeck`. 



## <a name="create-the-fragment"></a>Создания фрагмента

Каждая карта флэш-памяти будут управляться с помощью пользовательского интерфейса фрагмент вызывается `FlashCardFragment`. `FlashCardFragment`в представлении отображаются данные на одну плату флэш-памяти. Каждый экземпляр `FlashCardFragment` будут размещаться на `ViewPager`. 
`FlashCardFragment`в представлении будет состоять из `TextView` , отображается текст проблема флэш-карт. В этом представлении реализует обработчик событий, который использует `Toast` для отображения ответа, когда пользователь нажимает на вопрос флэш-карт. 



### <a name="create-the-flashcardfragment-layout"></a>Создание макета FlashCardFragment

Прежде чем `FlashCardFragment` может быть реализован, его макет должен быть определен. Это имеет разметку фрагмент контейнер для одного фрагмента. Добавить новую структуру для Android **ресурсы и макет** вызывается **flashcard_layout.axml**. Откройте **Resources/layout/flashcard_layout.axml** и замените его содержимое следующим кодом: 

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

Этот макет определяет одну плату flash фрагментом; Каждый фрагмент состоит из `TextView` , отображающий ошибку с использованием шрифта больших (100sp). Этот текст выравнивается по центру по вертикали и горизонтали на карте флэш-памяти. 



### <a name="create-the-initial-flashcardfragment-class"></a>Создайте исходный класс FlashCardFragment

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

Этот код заглушает основные `Fragment` определение, которое будет использоваться для отображения карты флэш-памяти. Обратите внимание, что `FlashCardFragment` является производным от версии библиотеки поддержки `Fragment` определенные в `Android.Support.V4.App.Fragment`. Конструктор является пустым, чтобы `newInstance` фабричный метод используется для создания нового `FlashCardFragment` вместо конструктора. 

`OnCreateView` Жизненного цикла метод создает и настраивает `TextView`. Он увеличивает макета для фрагмента `TextView` и возвращает увеличенную `TextView` вызывающему объекту. `LayoutInflater` и `ViewGroup` передаются `OnCreateView` , чтобы он может значительно увеличить макета. `savedInstanceState` Пакет содержит данные, `OnCreateView` использует для воссоздания `TextView` из сохраненного состояния. 

Представление фрагмента явно увеличивается при помощи вызова `inflater.Inflate`. `container` Аргумент является родительского представления и `false` флаг указывает, что преобразования отказ от Добавление увеличенную представления для родительского представления (она будет добавлена при `ViewPager` вызова этого адаптера `GetItem` метод позже в этом Пошаговое руководство). 



### <a name="add-state-code-to-flashcardfragment"></a>Добавьте код состояния FlashCardFragment

Как и действия, фрагмент не содержит `Bundle` используется для сохранения и получить его состояние. В **FlashCardPager**, то `Bundle` используется для сохранения вопроса и ответа на текст для связанного карты флэш-памяти. В **FlashCardFragment.cs**, добавьте следующий `Bundle` ключи в верхнюю часть `FlashCardFragment` определения класса: 

```csharp
private static string FLASH_CARD_QUESTION = "card_question";
private static string FLASH_CARD_ANSWER = "card_answer";
```

Изменить `newInstance` фабричный метод, чтобы он создает `Bundle` объекта и выше ключи используются для хранения переданный вопроса и ответа на текст в фрагменте после ее создания: 

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

Измените метод жизненного цикла фрагмент `OnCreateView` для получения этих сведений из пакета переданное и загрузить текст вопроса в `TextBox`: 

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

`answer` Переменная здесь не используется, но он будет использоваться позже при добавлении в этот файл код обработчика событий. 


## <a name="create-the-adapter"></a>Создание адаптера

`ViewPager` использует объект контроллера адаптера, который располагается между `ViewPager` и источником данных (показано на рисунке в ViewPager [адаптер](~/android/user-interface/controls/view-pager/index.md#adapter) статьи). Доступ к этим данным `ViewPager` необходимо указать пользовательский адаптер, производный от `PagerAdapter`. Так как в этом примере используется фрагментов, он использует `FragmentPagerAdapter` &ndash; `FragmentPagerAdapter` является производным от `PagerAdapter`. 
`FragmentPagerAdapter` Представляет каждую страницу в `Fragment` , хранится постоянно в диспетчере фрагментов для при условии, что пользователь может вернуться на страницу. Как вставляет пользователя по страницам `ViewPager`, `FragmentPagerAdapter` извлекает данные из источника данных и использует ее для создания `Fragment`s для `ViewPager` для отображения. 

При реализации `FragmentPagerAdapter`, необходимо переопределить следующие:

-   **Число** &ndash; только для чтения свойство, которое возвращает количество доступных представлений (страниц).

-   **GetItem** &ndash; возвращает фрагмент для отображения указанной страницы.

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

Этот код заглушает основные `FragmentPagerAdapter` реализации. В следующих разделах каждый из этих методов заменяется рабочего кода. Конструктор предназначен для передачи в диспетчере фрагментов `FlashCardDeckAdapter`в конструктор базового класса. 



### <a name="implement-the-adapter-constructor"></a>Реализуйте конструктор адаптера

Когда приложение создает экземпляр `FlashCardDeckAdapter`, он передает ссылку на диспетчер фрагментов и экземпляры `FlashCardDeck`. Добавьте следующую переменную члена в верхнюю часть `FlashCardDeckAdapter` класса в **FlashCardDeckAdapter.cs**: 

```csharp
public FlashCardDeck flashCardDeck;
```

Добавьте следующую строку кода, `FlashCardDeckAdapter` конструктор: 

```csharp
this.flashCardDeck = flashCards;
```

Эта строка кода хранилищ `FlashCardDeck` экземпляр `FlashCardDeckAdapter` будет использовать. 



### <a name="implement-count"></a>Реализуйте Count

`Count` Реализацию относительно проста: она возвращает номер flash карт в колоде флэш-карт. Замените `Count` следующим кодом: 

```csharp
public override int Count
{
    get { return flashCardDeck.NumCards; }
}
```


`NumCards` Свойство `FlashCardDeck` возвращает количество флэш-карты (число фрагментов) в наборе данных. 



### <a name="implement-getitem"></a>Реализуйте GetItem

`GetItem` Метод возвращает фрагмент, связанный с заданной позиции. Когда `GetItem` вызывается для положения в колоде флэш-карт, она возвращает `FlashCardFragment` отображать проблемы флэш-карт в этой позиции. Замените метод `GetItem` следующим кодом: 

```csharp
public override Android.Support.V4.App.Fragment GetItem(int position)
{
    return (Android.Support.V4.App.Fragment)
        FlashCardFragment.newInstance (
            flashCardDeck[position].Problem, flashCardDeck[position].Answer);
}
```

Этот код выполняет следующие задачи.

1.  Находит строку проблемы математические в `FlashCardDeck` колоде для заданной позиции. 

2.  Находит строку ответов в `FlashCardDeck` колоде для заданной позиции. 

3.  Вызовы `FlashCardFragment` фабричный метод `newInstance`, передавая строки проблемы и ответов флэш-карт. 

4.  Создает и возвращает новую карту flash `Fragment` , содержащее текст вопроса и ответа в этой позиции. 

При `ViewPager` отображает `Fragment` в `position`, он отображает `TextBox` содержится строка математической проблему, находящихся в `position` в колоде флэш-карт. 



## <a name="add-the-adapter-to-the-viewpager"></a>Добавление адаптера ViewPager

Теперь, когда `FlashCardDeckAdapter` будет реализован, пришло время, чтобы добавить его в `ViewPager`. В **MainActivity.cs**, добавьте следующую строку кода в конец `OnCreate` метод:

```csharp
FlashCardDeckAdapter adapter =
    new FlashCardDeckAdapter(SupportFragmentManager, flashCards);
viewPager.Adapter = adapter;
```

Этот код создает экземпляр `FlashCardDeckAdapter`, передавая `SupportFragmentManager` в качестве первого аргумента. ( `SupportFragmentManager` Свойство FragmentActivity используется для получения ссылки на `FragmentManager` &ndash; Дополнительные сведения о `FragmentManager`, в разделе [управление фрагментов](~/android/platform/fragments/managing-fragments.md).) 

Реализация основных компонентов завершена &ndash; построение и запуск приложения.
Вы увидите первое изображение колоде флэш-карт отображаются на экране, как показано на следующем снимке экрана слева. Проведите слева, чтобы увидеть больше карт флэш-памяти, затем проведите пальцем вправо для перемещения назад по колоде флэш-карт:

[![Снимки экрана примера приложения FlashCardPager без индикаторы страничного навигатора](viewpager-and-fragments-images/02-example-views-sml.png)](viewpager-and-fragments-images/02-example-views.png#lightbox)



## <a name="add-a-pager-indicator"></a>Добавить индикатор страничного навигатора

Этот минимальный `ViewPager` реализацию отображает каждый флэш-карт в колоде, но он обеспечивает где пользователь входит в колоде без причины. Следующим шагом является добавление `PagerTabStrip`. `PagerTabStrip` Сообщает пользователю о том, какие проблемы число отображается и предоставляет контекст навигации, отображая подсказку предыдущей и последующей карт флэш-памяти. 

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

При построении и запуске приложения вы увидите пустые `PagerTabStrip` в верхней части каждой карточки флэш-памяти: 

[![Крупный план PagerTabStrip без текста](viewpager-and-fragments-images/03-empty-pagetabstrip-sml.png)](viewpager-and-fragments-images/03-empty-pagetabstrip.png#lightbox)



### <a name="display-a-title"></a>Отображать заголовок

Чтобы добавить заголовок для каждой вкладки, реализовать `GetPageTitleFormatted` метода в адаптере. `ViewPager` вызовы `GetPageTitleFormatted` (Если реализована) для получения строки заголовка, описывающий страницы в указанной позиции. Добавьте следующий метод `FlashCardDeckAdapter` класса в **FlashCardDeckAdapter.cs**: 

```csharp
public override Java.Lang.ICharSequence GetPageTitleFormatted(int position)
{
    return new Java.Lang.String("Problem " + (position + 1));
}
```

Этот код преобразует позицию в колоде флэш-карт в число проблем. Результирующая строка преобразуется в Java `String` , возвращаемую `ViewPager`. При запуске приложения с помощью этого нового метода каждой страницы отображается номер проблемы в `PagerTabStrip`: 

[![Снимки экрана FlashCardPager с номером проблема наверху каждой страницы](viewpager-and-fragments-images/04-pagetabstrip-sml.png)](viewpager-and-fragments-images/04-pagetabstrip.png#lightbox)

Для просмотра номера проблему в колоде флэш-карт, который отображается в верхней части каждой карточки flash проведите выполняется и обратно. 



## <a name="handle-user-input"></a>Обработки вводимых пользователем данных

**FlashCardPager** представляет ряд фрагмент Молния видеоадаптеры в `ViewPager`, но она еще не способ выявить ответов для каждой задачи. В этом разделе добавляется обработчик событий `FlashCardFragment` для отображения ответа, когда пользователь нажимает на текст проблема флэш-карт. 

Откройте **FlashCardFragment.cs** и добавьте следующий код в конец `OnCreateView` метод непосредственно перед представления возвращается вызывающему объекту: 

```csharp
questionBox.Click += delegate
{
    Toast.MakeText(Activity.ApplicationContext,
            "Answer: " + answer, ToastLength.Short).Show();
};
```

Это `Click` обработчик событий отображает ответ в всплывающее уведомление, которое появляется при нажатии кнопки `TextBox`. `answer` Переменная была инициализирована ранее, когда сведения о состоянии было считано из пакета, который был передан в `OnCreateView`. Построение и запуск приложения, а затем проблемы текст на каждой карточке флэш-памяти для просмотра ответов: 

[![Снимки экрана FlashCardPager приложение всплывающие уведомления при касании ошибку](viewpager-and-fragments-images/05-answer-sml.png)](viewpager-and-fragments-images/05-answer.png#lightbox)

**FlashCardPager** в данном пошаговом руководстве использует `MainActivity` производными `FragmentActivity`, но можно также создавать производные `MainActivity` из `AppCompatActivity` (который также обеспечивает поддержку управления фрагментов). Чтобы просмотреть `AppCompatActivity` пример, в разделе [FlashCardPager](https://developer.xamarin.com/samples/monodroid/UserInterface%5CFlashCardPager/) в коллекции примеров. 



## <a name="summary"></a>Сводка

В этом пошаговом руководстве предоставленный пример с пошаговыми инструкциями по построению базового `ViewPager`-на базе приложения с использованием `Fragment`s. Оно представлено в источник данных примера, содержащий флэш-карт вопросы и ответы, `ViewPager` макет для отображения карты флэш-памяти и `FragmentPagerAdapter` подкласс, в котором подключается `ViewPager` к источнику данных. Чтобы помочь пользователю перемещаться по флэш-карты, входившие в инструкции, объясняется, как добавить `PagerTabStrip` для отображения числа проблемы в верхней части каждой страницы. Наконец код обработки событий был добавлен для отображения ответа, когда пользователь нажимает на проблему флэш-карт. 



## <a name="related-links"></a>Связанные ссылки

- [FlashCardPager (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/FlashCardPager)
