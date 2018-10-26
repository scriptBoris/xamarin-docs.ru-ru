---
title: Панели действий
ms.prod: xamarin
ms.assetid: 84A79F1F-9E73-4E3E-80FA-B72E5686900B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 5bb2349f141629478eb2dce995c7cbff5a954069
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50114617"
---
# <a name="actionbar"></a>Панели действий


## <a name="overview"></a>Обзор

При использовании `TabActivity`, код для создания значки вкладок не действует при использовании Android 4.0 framework. Несмотря на то, что функционально он работает, как и в версиях Android ниже 2.3, `TabActivity` сам класс был объявлен устаревшим в 4.0. Был введен новый способ создания интерфейс с вкладками, использующий панели действий, чем мы поговорим Далее.


## <a name="action-bar-tabs"></a>Вкладки панели действий

На панели действий включает в себя возможность добавить интерфейсы с вкладками в Android 4.0.
На следующем рисунке показан пример такой интерфейс.

[![Снимок экрана: приложение, работающее в эмуляторе; отображаются две вкладки](action-bar-images/25-actionbartabs.png)](action-bar-images/25-actionbartabs.png#lightbox)

Чтобы создать вкладки на панели действий, необходимо сначала задать его `NavigationMode` свойства для поддержки вкладки. В Android 4 `ActionBar` свойство доступно в классе действия, который можно использовать для задания `NavigationMode` следующим образом:

```csharp
this.ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
```

После этого, можно создать вкладку путем вызова `NewTab` метода на панели действий. С этим экземпляром вкладку, мы можем вызвать `SetText` и `SetIcon` методов, чтобы задать текст метки для вкладки и значком; эти вызовы выполняются в порядке, в коде, показанном ниже:

```csharp
var tab = this.ActionBar.NewTab ();
tab.SetText (tabText);
tab.SetIcon (Resource.Drawable.ic_tab_white);
```

Прежде чем мы можем добавить вкладку тем не менее, необходимо обрабатывать `TabSelected` событий. В этом обработчике мы можем создать содержимое для вкладки. Вкладки панели действий, предназначены для работы с *фрагментов*, которые являются классы, представляющие части пользовательского интерфейса в действии. В этом примере фрагмент представление содержит один `TextView`, который мы расширения в наших `Fragment` подкласс следующим образом:

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

Аргумент события, передаваемый в `TabSelected` событие имеет тип `TabEventArgs`, который включает `FragmentTransaction` свойство, которое можно использовать для добавления фрагмента, как показано ниже:

```csharp
tab.TabSelected += delegate(object sender, ActionBar.TabEventArgs e) {             
    e.FragmentTransaction.Add (Resource.Id.fragmentContainer,
        new SampleTabFragment ());
};
```

Наконец, можно добавить вкладку в панели действий путем вызова `AddTab` метод, как показано в этом коде:

```csharp
this.ActionBar.AddTab (tab);
```

Полный пример см. в разделе *HelloTabsICS* проекта в примере кода для этого документа.


## <a name="shareactionprovider"></a>ShareActionProvider

`ShareActionProvider` Класс позволяет доступом действие должно выполняться из панели действий. Она отвечает за создание представления действий со списком приложений, которые может обрабатывать доступом намерения и ведет журнал ранее использовавшихся приложений для быстрого доступа к ним позже на панели действий. Это позволяет приложениям обмениваться данными с помощью пользовательским интерфейсом, который используется на протяжении всего Android.


### <a name="image-sharing-example"></a>Пример совместного использования образа

Например, ниже приведен снимок экрана панели действий с помощью пункта меню для совместного использования образа (из [ShareActionProvider](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/) пример). Когда пользователь касается элемента меню на панели действий, ShareActionProvider загружает в приложении для обработки объекта Intent, с которым связан `ShareActionProvider`. В этом примере приложение для обмена сообщениями уже использовалось, чтобы оно выводится на панели действий.

[![Снимок экрана обмена сообщениями значок приложения на панели действий](action-bar-images/09-shareactionprovider.png)](action-bar-images/09-shareactionprovider.png#lightbox)


Когда пользователь щелкает элемент в панели действий, обмена сообщениями, содержащий общего образа запускается приложение, как показано ниже:

[![Снимок экрана: отображение изображения monkey приложении для обмена сообщениями](action-bar-images/10-messagewithimage.png)](action-bar-images/10-messagewithimage.png#lightbox)


### <a name="specifying-the-action-provider-class"></a>Указав действие класс поставщика

Чтобы использовать `ShareActionProvider`, задайте `android:actionProviderClass` атрибут для элемента в XML для меню на панели действий, следующим образом:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
  <item android:id="@+id/shareMenuItem"
      android:showAsAction="always"
      android:title="@string/sharePicture"
      android:actionProviderClass="android.widget.ShareActionProvider" />
</menu>
```


### <a name="inflating-the-menu"></a>Подобных меню

Для расширения в меню, мы переопределяем `OnCreateOptionsMenu` в подкласс действия. Получив ссылку на меню, мы можем получить `ShareActionProvider` из `ActionProvider` свойство пункта меню и затем использовать метод SetShareIntent для задания `ShareActionProvider`его намерения, как показано ниже:

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


### <a name="creating-the-intent"></a>Создание цели

`ShareActionProvider` Будет использовать намерения, передаваемый `SetShareIntent` метод в приведенном выше коде, чтобы запустить соответствующее действие. В этом случае мы создадим объекта Intent, чтобы отправить изображение, используя следующий код:

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

Изображение в приведенном выше примере кода включена как ресурс с приложением и копируются в общедоступные расположение при создании действия, поэтому он будет доступен для других приложений, таких как приложения обмена сообщениями. Полный исходный этого примера, демонстрирующая ее использование содержится пример кода, сопровождающий эту статью.



## <a name="related-links"></a>Связанные ссылки

- [Hello вкладки ICS (пример)](https://developer.xamarin.com/samples/HelloTabsICS/)
- [Демонстрация ShareActionProvider (пример)](https://developer.xamarin.com/samples/monodroid/ShareActionProviderDemo/)
- [Знакомство с Ice Cream Sandwich](http://www.android.com/about/ice-cream-sandwich/)
- [Платформа Android 4.0](http://developer.android.com/sdk/android-4.0.html)
