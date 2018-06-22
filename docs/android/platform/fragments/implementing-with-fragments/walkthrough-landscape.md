---
title: Фрагменты Пошаговое руководство — часть 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 04/26/2018
ms.openlocfilehash: 58291388d375a4fd9273c8e0cd46db3799966766
ms.sourcegitcommit: e16517edcf471b53b4e347cd3fd82e485923d482
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33798916"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Пошаговое руководство фрагменты &ndash; альбомной ориентации

[Пошаговое руководство по фрагментам &ndash; часть 1](./walkthrough.md) было показано, как создавать и использовать фрагменты в Android приложения, ориентированного на экране меньшего размера на телефоне. Далее в этом пошаговом руководстве необходимо изменить приложение, чтобы воспользоваться преимуществами дополнительных горизонтальный интервал на планшете &ndash; будет одно действие, которое всегда будет иметь список воспроизведения ( `TitlesFragment`) и `PlayQuoteFragment` будет динамически добавлен для действия в ответ на выбор пользователя:

[![Приложение, работающее на планшете](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Телефоны под управлением в альбомной ориентации также будет обеспечен этого компонента:

[![Приложение, работающее на телефоне с Android в альбомной ориентации](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Идет обновление приложения для обработки альбомной ориентации

Следующие изменения будут созданы на основе работы, выполненной в [Пошаговое руководство по фрагментам - телефон](./walkthrough.md)

1. Создание альтернативного макет, чтобы отобразить обе `TitlesFragment` и `PlayQuoteFragment`.
1. Обновление `TitlesFragment` для обнаружения, если устройство отображается обоих фрагментах одновременно и соответствующим образом изменить поведение.
1. Обновление `PlayQuoteActivity` для закрытия, когда устройство находится в режиме альбомной ориентации.

## <a name="1-create-an-alternate-layout"></a>1. Создание альтернативного макета

При создании главной действия на устройстве с Android Android решить, какие макета для загрузки в зависимости от ориентации устройства. По умолчанию будет предоставлять Android **Resources/layout/activity_main.axml** файл макета. Для устройств, которые загружаются в альбомном режиме предоставит Android **Resources/layout-land/activity_main.axml** файл макета. Руководство по [Android ресурсов](/xamarin/android/app-fundamentals/resources-in-android) содержит дополнительные сведения о том, как Android определяет, какой ресурс файлы для загрузки приложения.

Создать структуру альтернативный, предназначенное **Альбомная** ориентации, выполнив действия, описанные в [альтернативные макеты](/xamarin/android/user-interface/android-designer/alternative-layout-views) руководства. Это следует добавить в проект новый файл ресурсов макета **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![Альтернативный макета в обозревателе решений](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

[![Альтернативный макет в области решения](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

После создания альтернативный макета, изменить исходный файл **Resources/layout-land/activity_main.axml** , чтобы он соответствовал этот XML-код:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/two_fragments_layout"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <fragment android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_weight="1"
        android:layout_width="0px"
        android:layout_height="match_parent" />

    <FrameLayout android:id="@+id/playquote_container"
            android:layout_weight="1"
            android:layout_width="0px"
            android:layout_height="match_parent"
             />
</LinearLayout>
```

Получает идентификатор ресурса представление корневого действия `two_fragments_layout` и имеет два вложенных представления, `fragment` и `FrameLayout`. Хотя `fragment` загружается статически, `FrameLayout` выступает в качестве «заполнитель», будут заменены во время выполнения, `PlayQuoteFragment`. Каждый раз, в выбран новый play `TitlesFragment`, `playquote_container` будет добавлено в новый экземпляр `PlayQuoteFragment`.

Каждое из представлений вложенных займет всю высоту родительского элемента. Ширина каждого вложенное представление управляется `android:layout_weight` и `android:layout_width` атрибуты. В этом примере каждый вложенное представление будет занимать 50% от ширины предоставляют родительской задачей. В разделе [Google документ на LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) для получения сведений об _макета вес_.

## <a name="2-changes-to-titlesfragment"></a>2. Изменения в TitlesFragment

После создания макета альтернативный бывает необходимо обновить `TitlesFragment`. Когда приложение отображает два фрагмента на одно действие, затем `TitlesFragment` следует загружать `PlayQuoteFragment` в родительском объекте действия. В противном случае `TitlesFragment` должен быть запущен `PlayQuoteActivity` узлов, которые `PlayQuoteFragment`. Логический флаг поможет `TitlesFragment` определить поведение следует использовать. Этот флаг будет инициализирована в `OnActivityCreated` метод.

Во-первых, добавьте экземпляр переменной в верхней части `TitlesFragment` класса:

```csharp
bool showingTwoFragments;
```

Добавьте следующий фрагмент кода для `OnActivityCreated` для инициализации переменной: 

```csharp
var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
showingTwoFragments = quoteContainer != null &&
                      quoteContainer.Visibility == ViewStates.Visible;
if (showingTwoFragments)
{
    ListView.ChoiceMode = ChoiceMode.Single;
    ShowPlayQuote(selectedPlayId);
}
```

Если устройство работает в альбомном режиме, то `FrameLayout` с Идентификатором ресурса `playquote_container` будут видны на экране, поэтому `showingTwoFragments` будут присвоены `true`. Если устройство работает в книжной ориентации, затем `playquote_container` не будет на экране, поэтому `showingTwoFragments` будет `false`.

`ShowPlayQuote` Метод потребуется изменить отображение предложения &ndash; в фрагменте или запуска нового действия.  Обновление `ShowPlayQuote` метод, чтобы загрузить фрагмент при отображении двух фрагментов, в противном случае следует запустить действие:

```csharp
void ShowPlayQuote(int playId)
{
    selectedPlayId = playId;
    if (showingTwoFragments)
    {
        ListView.SetItemChecked(selectedPlayId, true);

        var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

        if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
        {
            var container = Activity.FindViewById(Resource.Id.playquote_container);
            var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

            FragmentTransaction ft = FragmentManager.BeginTransaction();
            ft.Replace(Resource.Id.playquote_container, quoteFrag);
            ft.Commit();
        }
    }
    else
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Если пользователь выбрал play, которая отличается от той, которая в настоящий момент отображается в `PlayQuoteFragment`, затем новый `PlayQuoteFragment` создается и заменит содержимое `playquote_container` в контексте `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Полный код для TitlesFragment

После завершения всех предыдущих изменений `TitlesFragment`, полный класс должен соответствовать этот код:

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;
    bool showingTwoFragments;

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<string>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }


        var quoteContainer = Activity.FindViewById(Resource.Id.playquote_container);
        showingTwoFragments = quoteContainer != null &&
                                quoteContainer.Visibility == ViewStates.Visible;
        if (showingTwoFragments)
        {
            ListView.ChoiceMode = ChoiceMode.Single;
            ShowPlayQuote(selectedPlayId);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        selectedPlayId = playId;
        if (showingTwoFragments)
        {
            ListView.SetItemChecked(selectedPlayId, true);

            var playQuoteFragment = FragmentManager.FindFragmentById(Resource.Id.playquote_container) as PlayQuoteFragment;

            if (playQuoteFragment == null || playQuoteFragment.PlayId != playId)
            {
                var container = Activity.FindViewById(Resource.Id.playquote_container);
                var quoteFrag = PlayQuoteFragment.NewInstance(selectedPlayId);

                FragmentTransaction ft = FragmentManager.BeginTransaction();
                ft.Replace(Resource.Id.playquote_container, quoteFrag);
                ft.AddToBackStack(null);
                ft.SetTransition(FragmentTransit.FragmentFade);
                ft.Commit();
            }
        }
        else
        {
            var intent = new Intent(Activity, typeof(PlayQuoteActivity));
            intent.PutExtra("current_play_id", playId);
            StartActivity(intent);
        }
    }
}
```

## <a name="3-changes-to-playquoteactivity"></a>3. Изменения в PlayQuoteActivity

Имеется один последний элемент данных для обеспечения проверки: `PlayQuoteActivity` не требуется, если устройство находится в режиме альбомной ориентации. Если устройство находится в режиме альбомной ориентации `PlayQuoteActivity` не должны быть видимыми. Обновление `OnCreate` метод `PlayQuoteActivity` , чтобы он сам закрывается. Данный пример кода является окончательной версии `PlayQuoteActivity.OnCreate`:

```csharp
protected override void OnCreate(Bundle savedInstanceState)
{
    base.OnCreate(savedInstanceState);

    if (Resources.Configuration.Orientation == Android.Content.Res.Orientation.Landscape)
    {
        Finish();
    }

    var playId = Intent.Extras.GetInt("current_play_id", 0);
    var playQuoteFrag = PlayQuoteFragment.NewInstance(playId);
    FragmentManager.BeginTransaction()
                    .Add(Android.Resource.Id.Content, playQuoteFrag)
                    .Commit();
}
```

Это изменение добавляет проверку для ориентации устройства. Если он находится в альбомном режиме, затем `PlayQuoteActivity` будет закрытия.

## <a name="4-run-the-application"></a>4. Запуск приложения

После этих изменений завершена, запуск приложения, поворот устройства альбомная режиме (при необходимости) и выберите воспроизведения. Квоты должны отображаться на экране список воспроизведения:

[![Приложение, работающее на телефоне с Android в альбомной ориентации](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Приложение, работающее на планшете Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
