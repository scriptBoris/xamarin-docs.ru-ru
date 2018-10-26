---
title: Макеты с вкладками с ActionBar
description: В этом руководстве представлены и объясняется, как использовать API панели действий для создания с вкладками пользовательского интерфейса в приложениях Xamarin.Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: af5554d08ac6c45fc0c392bd17cef5d91251bb1a
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106511"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Макеты с вкладками с ActionBar

_В этом руководстве представлены и объясняется, как использовать API панели действий для создания с вкладками пользовательского интерфейса в приложениях Xamarin.Android._


## <a name="overview"></a>Обзор

На панели действий — это шаблон Android пользовательского интерфейса, который используется для обеспечения согласованного пользовательского интерфейса для основных компонентов, таких как вкладки, удостоверение приложения, меню и поиска. В Android 3.0 (уровень API 11) Google представила ActionBar API-интерфейсы для платформы Android. API-интерфейсы ActionBar вводят темы пользовательского интерфейса для обеспечения согласованного внешнего вида и поведения и классы, которые позволяют с вкладками пользовательских интерфейсов. В этом руководстве описывается добавление вкладок панели действий приложения Xamarin.Android. Также описывается использование библиотеки поддержки Android версии 7 в знаки табуляции панели действий бэкпортировать для приложений Xamarin.Android, предназначенных для Android 2.1 для Android 2.3. 

Обратите внимание, что `Toolbar` является компонентом панели более нового и более обобщенное действия, который следует использовать вместо из `ActionBar` (`Toolbar` предназначен для замены `ActionBar`). Дополнительные сведения см. в разделе [инструментов](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Требования

Любое приложение Xamarin.Android, которое предназначено для API уровня 11 (Android 3.0) или более поздней версии имеет доступ к API-интерфейсы панели действий в рамках собственных API Android. 

Некоторые интерфейсы API, ActionBar перенесенных обратно в API уровня 7 (Android 2.1) и доступны через [библиотека AppCompat V7](http://developer.android.com/tools/support-library/features.html#v7-appcompat), становится доступным для приложений Xamarin.Android с помощью [Xamarin Android библиотека поддержки - версии 7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) пакета.



## <a name="introducing-tabs-in-the-actionbar"></a>Общие сведения о вкладках в панели действий

На панели действий пытается одновременно отображаться все ее вкладки и сделать все вкладки равными в размер в зависимости от широкого метку вкладки. Это показано на следующем снимке экрана: 

![Снимок экрана с примером из панели действий с помощью всех вкладок одинакового размера показано](with-action-bar-images/image1.png)

При ActionBar не может отобразить все вкладки, которая настроит вкладки в виде поддерживающим горизонтальную прокрутку. Пользователь может проведите пальцем влево или вправо, чтобы увидеть остальные вкладки. На этом снимке экрана из Google Play показан пример этого. 

![Снимок экрана с примером из вкладок в виде горизонтальной прокрутки](with-action-bar-images/image2.png)

Каждая вкладка в панели действий должен быть связан [ *фрагмент*](~/android/platform/fragments/index.md). Когда пользователь выбирает вкладку, в приложении отображается фрагмент, который связан с вкладкой. ActionBar не отвечает за отображение соответствующего фрагмента для пользователя. Вместо этого ActionBar будет уведомлять приложения об изменениях состояния на вкладке через класс, реализующий интерфейс ActionBar.ITabListener. Этот интерфейс предоставляет три метода обратного вызова, которые Android будет вызывать при изменении состояния вкладки: 

-  **OnTabSelected** -этот метод вызывается, когда пользователь выбирает вкладку. Оно должно отображать фрагмента.

-  **OnTabReselected** -этот метод вызывается, когда вкладке уже выбран, но повторном выборе пользователем. Этот обратный вызов обычно используется для обновления или обновления отображаемого фрагмента.

-  **OnTabUnselected** -этот метод вызывается, когда пользователь выбирает другой вкладке. Этот обратный вызов используется для сохранения состояния в отображаемое фрагменте, прежде чем он исчезнет.

Создает оболочку для Xamarin.Android `ActionBar.ITabListener` с событиями на `ActionBar.Tab` класса. Приложения могут назначить обработчики событий к одному или нескольким из этих событий. Существует три события (один для каждого метода в `ActionBar.ITabListener`), вкладка «Действие» панели вызовет: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Добавление вкладок в панели действий

ActionBar собственного Android (уровень API 11) версии 3.0 и более поздней версии и доступна в любое приложение Xamarin.Android, предназначенного для этого API, как минимум. 

Добавление вкладки панели действий действие Android сделайте следующее: 

1. В `OnCreate` метод действия &ndash; *перед инициализацией любой пользовательский Интерфейс мини-приложения* &ndash; приложение должно задать `NavigationMode` на `ActionBar` для `ActionBar.NavigationModeTabs` как показано в этом коде фрагмент кода:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Создать новую вкладку, используя `ActionBar.NewTab()`.

3. Назначаются обработчики событий или предоставить пользовательскую `ActionBar.ITabListener` реализация, которая будет отвечать на события, которые вызываются, когда пользователь взаимодействует с вкладками панели действий.

4. Добавить вкладку, которая была создана в предыдущем шаге, чтобы `ActionBar`.


Ниже приведен пример использования эти шаги для добавления вкладок для приложения, использующего обработчики событий для реагирования на изменения состояния. 

```csharp
protected override void OnCreate(Bundle bundle)
{
    ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
    SetContentView(Resource.Layout.Main);

    ActionBar.Tab tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab1_text));
    tab.SetIcon(Resource.Drawable.tab1_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);

    tab = ActionBar.NewTab();
    tab.SetText(Resources.GetString(Resource.String.tab2_text));
    tab.SetIcon(Resource.Drawable.tab2_icon);
    tab.TabSelected += (sender, args) => {
                           // Do something when tab is selected
                       };
    ActionBar.AddTab(tab);
}
```


#### <a name="event-handlers-vs-actionbaritablistener"></a>Vs обработчики событий ActionBar.ITabListener

Приложения должны использовать обработчики событий и `ActionBar.ITabListener` для разных сценариев. Обработчики событий также имеют определенный объем синтаксического удобства. они спасти от того, чтобы создать класс и реализуйте `ActionBar.ITabListener`. Данное преимущество некоторых затрат &ndash; Xamarin.Android выполняет это преобразование, создание одного класса и реализация `ActionBar.ITabListener` для вас. Это нормально, если приложение имеет ограниченное число вкладок. 

При работе с слишком большого числа вкладок или совместный доступ общую функциональность между вкладками панели действий, он может оказаться более эффективным, таких как память и производительность, чтобы создать пользовательский класс, реализующий `ActionBar.ITabListener`и совместное использование одного экземпляра класса. Это позволит снизить количество gref, Установленное по, использующего приложения Xamarin.Android. 



### <a name="backwards-compatibility-for-older-devices"></a>Обратная совместимость для более старых устройств

[Библиотеки поддержки Android версии 7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) назад порты вкладок панели действий, чтобы Android 2.1 (API уровня 7). Вкладки доступны в приложениях Xamarin.Android, после добавления этого компонента в проект.

Чтобы использовать панели действий, мероприятие необходимо подкласс `ActionBarActivity` и использования AppCompat темы, как показано в следующем фрагменте кода:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Действие может получить ссылку на его панели действий из `ActionBarActivity.SupportingActionBar` свойство. В следующем фрагменте кода показан пример настройки панели действий в действии:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity : ActionBarActivity, ActionBar.ITabListener
{
    static readonly string Tag = "ActionBarTabsSupport";

    public void OnTabReselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Optionally refresh/update the displayed tab.
        Log.Debug(Tag, "The tab {0} was re-selected.", tab.Text);
    }

    public void OnTabSelected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Display the fragment the user should see
        Log.Debug(Tag, "The tab {0} has been selected.", tab.Text);
    }

    public void OnTabUnselected(ActionBar.Tab tab, FragmentTransaction ft)
    {
        // Save any state in the displayed fragment.
        Log.Debug(Tag, "The tab {0} as been unselected.", tab.Text);
    }

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        SupportActionBar.NavigationMode = ActionBar.NavigationModeTabs;
        SetContentView(Resource.Layout.Main);
    }

    void AddTabToActionBar(int labelResourceId, int iconResourceId)
    {
        ActionBar.Tab tab = SupportActionBar.NewTab()
                                            .SetText(labelResourceId)
                                            .SetIcon(iconResourceId)
                                            .SetTabListener(this);
        SupportActionBar.AddTab(tab);
    }
}
```


## <a name="summary"></a>Сводка

В этом руководстве мы рассмотрели, как создать интерфейс с вкладками в Xamarin.Android с помощью панели действий. Были рассмотрены способы добавления вкладки панели действий и взаимодействие с вкладки событий с помощью действия `ActionBar.ITabListener` интерфейс. Мы также увидели, как библиотеки поддержки Android версии 7 AppCompat пакета бэкпортированным ActionBar знаки табуляции в более старых версиях Android. 


## <a name="related-links"></a>Связанные ссылки

- [ActionBarTabs (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [ToolBar](~/android/user-interface/controls/tool-bar/index.md)
- [Фрагменты](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Шаблон панели действий](http://developer.android.com/design/patterns/actionbar.html)
- [AppCompat Android версии 7](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Пакет NuGet версии 7 AppCompat для поддержки Xamarin.Android библиотеки](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
