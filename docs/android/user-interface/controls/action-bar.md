---
title: Панели действий
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 29726450e0899177f3223deeade1cb4766d933a5
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30769098"
---
# <a name="actionbar"></a>Панели действий


## <a name="overview"></a>Обзор

При использовании `TabActivity`, код для создания вкладки значки игнорируется при запуске на платформе Android 4.0 framework. Несмотря на то, что функционально он работает, как и в версиях Android перед 2.3, `TabActivity` сам класс рекомендуется к использованию в версии 4.0. Введен новый способ создания интерфейс с вкладками, использующий панели действий будут обсуждаться далее.


## <a name="action-bar-tabs"></a>Вкладки панели действий

Панели действий поддерживает добавление интерфейсы с вкладками в Android 4.0.
На следующей картинке показан пример такого интерфейса.

[![Снимок экрана приложения в эмуляторе; отображаются две вкладки](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Чтобы создать вкладки в области действия, необходимо сначала установить его `NavigationMode` свойства для поддержки вкладок. В Android 4 `ActionBar` свойство доступно на класс действия, которые можно использовать для задания `NavigationMode` следующим образом:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

После этого, можно создать вкладку путем вызова `NewTab` метода на панели действий. С этим экземпляром вкладку можно вызвать `SetText` и `SetIcon` методы для установки на вкладке текст метки и значок; эти вызовы осуществляются в приведенный ниже код в порядке:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Прежде чем мы можем добавить вкладку тем не менее, необходимо обрабатывать `TabSelected` событий. В этом обработчике можно создать содержимое для вкладки. Панель действий вкладки предназначены для работы с *фрагментов*, которые являются классы, представляющие часть пользовательского интерфейса в действии. Например, представление этот фрагмент содержит один `TextView`, который мы увеличению в нашей `Fragment` подкласс следующим образом:

```csharp
class SampleTabFragment: Fragment
{           
    public override View OnCreateView (LayoutInflater inflater,
        ViewGroup container, Bundle savedInstanceState)
    {
        base.OnCreateView (inflater, container, savedInstanceState);
       
        var view = inflater.Inflate (
            Resource.Layout.Tab, container, false);

        var sampleTextView =
            view.FindViewById<TextView> (Resource.Id.sampleTextView);            
        sampleTextView.Text = "sample fragment text";


        return view;
    }
}
```

Переданный аргумент события `TabSelected` событие имеет тип `TabEventArgs`, которая содержит `FragmentTransaction` свойство, которое можно использовать для добавления в фрагменте, как показано ниже:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Наконец, мы можем добавить вкладку панели действий путем вызова `AddTab` метода, как показано в этом коде:

```csharp
this.ActionBar.AddTab (tab);
```

Полный пример см. в разделе *HelloTabsICS* проекта в образце кода для этого документа.


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider` Класс позволяет доступом действие должно выполняться с панели действий. Он отвечает за создание представления действие со списком приложений, которые могут обрабатывать доступом назначение и ведет журнал ранее использовавшихся приложений для упрощения доступа к ним позже с панели действий. Это позволяет приложениям обмениваться данными через пользовательский интерфейс, используется на протяжении всего Android.


### <a name="image-sharing-example"></a>Пример совместного использования изображения

Например, ниже приведен снимок экрана на панели действий с элементом меню для совместного использования изображения (взяты из [ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/) пример). Когда пользователь нажимает элемент меню на панели действий, ShareActionProvider загружает в приложении для обработки, связанный с целью `ShareActionProvider`. В этом примере приложения обмена сообщениями ранее использовалась, поэтому оно выводится на панели действий.

[![Снимок экрана значок приложения в области действия обмена сообщениями](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Когда пользователь щелкает элемент в панели действий, обмена сообщениями, содержащий общий образ запускается приложение, как показано ниже:

[![Снимок экрана приложения обмена сообщениями, отображении monkey изображения](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Указывает действие, класс поставщика

Для использования `ShareActionProvider`, задайте `android:actionProviderClass` атрибут в XML для меню, панели действий пункт меню следующим образом:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>Дало бы ошибку в меню

К увеличению меню, переопределите `OnCreateOptionsMenu` в подкласс действия. Когда у нас есть ссылка в меню, мы можем получить `ShareActionProvider` из `ActionProvider` свойства элемента меню, а затем с помощью метода SetShareIntent для задания `ShareActionProvider`в назначение, как показано ниже:

```csharp
public override bool OnCreateOptionsMenu (IMenu menu)
{
    MenuInflater.Inflate (Resource.Menu.ActionBarMenu, menu);       
           
    var shareMenuItem = menu.FindItem (Resource.Id.shareMenuItem);           
    var shareActionProvider =
       (ShareActionProvider)shareMenuItem.ActionProvider;
    shareActionProvider.SetShareIntent (CreateIntent ());
}
```


### <a name="creating-the-intent"></a>Создание, назначение

`ShareActionProvider` Будет использовать назначение, передаваемый `SetShareIntent` метод в приведенном выше коде, чтобы запустить соответствующее действие. В этом случае мы создадим попытка отправить изображение, используя следующий код:

```csharp
Intent CreateIntent ()
{  
    var sendPictureIntent = new Intent (Intent.ActionSend);
    sendPictureIntent.SetType ("image/*");
    var uri = Android.Net.Uri.FromFile (GetFileStreamPath ("monkey.png"));          
    sendPictureIntent.PutExtra (Intent.ExtraStream, uri);
    return sendPictureIntent;
}
```

Изображение в приведенном выше примере кода включена как ресурс-контейнер с приложением и копируется в общедоступном расположении при создании действия, поэтому будут доступны для других приложений, таких как приложения обмена сообщениями. В образце кода к этой статье содержится полный исходный этот пример, иллюстрирующий его использования.



## <a name="related-links"></a>Связанные ссылки

- [ICS вкладки Hello (пример)](https://developer.xamarin.com/samples/HelloTabsICS/)
- [Демонстрация ShareActionProvider (пример)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [Знакомство с приложением Сандвичевы мороженого](http://www.android.com/about/ice-cream-sandwich/)
- [Android 4.0 платформы](http://developer.android.com/sdk/android-4.0.html)
