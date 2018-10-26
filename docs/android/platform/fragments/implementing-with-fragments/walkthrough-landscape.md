---
title: Фрагменты Пошаговое руководство. часть 2
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: 444A894D-5197-4726-934F-79BA80A71CB0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/26/2018
ms.openlocfilehash: 7ec8ad6ce428107d2255dd07c7e69c9e77780c09
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50118244"
---
# <a name="fragments-walkthrough-ndash-landscape"></a>Фрагменты пошаговое &ndash; альбомной ориентации

[Пошаговое руководство по фрагментам &ndash; часть 1](./walkthrough.md) было показано, как создать и использовать фрагменты в Android приложение, предназначенное для небольших экранов на телефоне. Далее в этом пошаговом руководстве необходимо изменить приложение, чтобы воспользоваться преимуществами очень горизонтальный интервал на планшете &ndash; будет существовать одно действие, всегда будет список воспроизведения ( `TitlesFragment`) и `PlayQuoteFragment` будут добавляться динамически действие в ответ на выбор, сделанный пользователем:

[![Приложение, работающее на планшете](./walkthrough-landscape-images/01-tablet-screenshot-sml.png)](./walkthrough-landscape-images/01-tablet-screenshot.png#lightbox)

Это расширение также преимущества телефонов, которые выполняются в альбомном режиме.

[![Приложение, запущенное на телефоне с Android в альбомном режиме](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

## <a name="updating-the-app-to-handle-landscape-orientation"></a>Изменение приложения для обработки альбомной ориентации

Следующие изменения будут созданы на основе работы, выполненной в [Пошаговое руководство по фрагментам - телефона](./walkthrough.md)

1. Создавать альтернативный макет для отображения оба `TitlesFragment` и `PlayQuoteFragment`.
1. Обновление `TitlesFragment` для обнаружения, если устройство отображается как фрагменты одновременно и соответствующим образом изменить поведение.
1. Обновление `PlayQuoteActivity` закрыть, когда устройство находится в альбомной ориентации.

## <a name="1-create-an-alternate-layout"></a>1. Создавать альтернативные макет

При создании действия Main на устройстве Android, Android, определит, какой макет следует загрузить зависимости от их ориентации устройства. По умолчанию предоставляет Android **Resources/layout/activity_main.axml** файл макета. Для устройств, которые загружают в альбомном режиме предоставит Android **Resources/layout-land/activity_main.axml** файл макета. Руководство по [ресурсы Android](/xamarin/android/app-fundamentals/resources-in-android) содержит дополнительные сведения о том, как Android определяет, какой ресурс файлы для загрузки для приложения.

Создавать альтернативные макет, ориентированного **Альбомная** ориентации, выполнив действия, описанные в [альтернативным структурам](/xamarin/android/user-interface/android-designer/alternative-layout-views) руководства. Это следует добавить в проект новый файл ресурсов макета **Resources/layout/activity_main.axml**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![Альтернативный макет в обозревателе решений](./walkthrough-landscape-images/02-alternate-layout.w157-sml.png)](./walkthrough-landscape-images/02-alternate-layout.w157.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

[![Альтернативный макет на панели решения](./walkthrough-landscape-images/02-alternate-layout.m743-sml.png)](./walkthrough-landscape-images/02-alternate-layout.m743.png#lightbox)

-----

Создав альтернативный макет, изменить исходный файл **Resources/layout-land/activity_main.axml** , чтобы он соответствовал этот XML-код:

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

Получает идентификатор ресурса корневое представление действия `two_fragments_layout` и имеет два вложенных представлений, `fragment` и `FrameLayout`. Хотя `fragment` загружается статически, `FrameLayout` выступает в роли «заполнитель», будут заменены во время выполнения путем `PlayQuoteFragment`. Каждый раз, в выбран новый play `TitlesFragment`, `playquote_container` будет добавлено в новый экземпляр класса `PlayQuoteFragment`.

Каждое из этих вложенных представлений займет всю высоту родительского элемента. Ширина каждого вложенное представление управляется `android:layout_weight` и `android:layout_width` атрибуты. В этом примере каждый вложенное представление будет занимать 50% от ширины предоставляют к родительскому элементу. См. в разделе [Google документ на LinearLayout](https://developer.android.com/guide/topics/ui/layout/linear.html) подробные сведения о _вес макета_.

## <a name="2-changes-to-titlesfragment"></a>2. Изменения в TitlesFragment

После создания альтернативного макета, она необходима для обновления `TitlesFragment`. Если приложение отображает два фрагмента на одно действие, затем `TitlesFragment` должны загрузиться `PlayQuoteFragment` в родительском объекте действия. В противном случае `TitlesFragment` должен запуститься `PlayQuoteActivity` узел `PlayQuoteFragment`. Логический флаг поможет `TitlesFragment` определить, какое поведение, следует использовать. Этот флаг будет инициализироваться в `OnActivityCreated` метод.

Во-первых, добавьте переменную экземпляра в верхней части `TitlesFragment` класса:

```csharp
bool showingTwoFragments;
```

Затем добавьте следующий фрагмент кода для `OnActivityCreated` для инициализации переменной: 

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

Если устройство работает под управлением в альбомном режиме, то `FrameLayout` с Идентификатором ресурса `playquote_container` будут видны на экране, чтобы `showingTwoFragments` будет инициализироваться `true`. Если устройство работает в книжной ориентации, затем `playquote_container` не будет на экране, поэтому `showingTwoFragments` будет `false`.

`ShowPlayQuote` Метод нужно будет изменить отображение цитаты &ndash; фрагмент или запуска нового действия.  Обновление `ShowPlayQuote` метод, чтобы загрузить фрагмент при отображении двух фрагментов, в противном случае он должен запустить действие:

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

Если пользователь выбрал play, которая отличается от той, которая отображается в настоящий момент в `PlayQuoteFragment`, затем новый `PlayQuoteFragment` создается и заменит содержимое `playquote_container` в контексте `FragmentTransaction`.

### <a name="complete-code-for-titlesfragment"></a>Полный код для TitlesFragment

После завершения всех предыдущих изменений `TitlesFragment`, завершенный класс должен соответствовать этот код:

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

Один конечный подробные сведения для обеспечения проверки: `PlayQuoteActivity` не требуется, когда устройство находится в альбомной ориентации. Если устройство находится в альбомном режиме `PlayQuoteActivity` не должны быть видимыми. Обновление `OnCreate` метод `PlayQuoteActivity` таким образом, чтобы он будет завершить свою работу. Этот код является окончательной версии `PlayQuoteActivity.OnCreate`:

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

Это изменение добавляет проверку для ориентации устройства. Если он находится в альбомной ориентации, затем `PlayQuoteActivity` будет завершить свою работу.

## <a name="4-run-the-application"></a>4. Запуск приложения

После этих изменение не завершится, запустите приложение, повороте устройства на альбомную режиме (при необходимости), а затем выберите воспроизведения. Квоты должны отображаться на экране как список воспроизведения:

[![Приложение, запущенное на телефоне с Android в альбомном режиме](./images/intro-screenshot-phone-land-sml.png)](./images/intro-screenshot-phone-land.png#lightbox)

[![Приложение, работающее на планшете Android](./images/intro-screenshot-tablet-sml.png)](./images/intro-screenshot-tablet.png#lightbox)
