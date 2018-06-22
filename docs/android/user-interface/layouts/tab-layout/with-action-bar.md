---
title: Макеты с вкладками с панели действий
description: В этом руководстве представлены и объясняется, как использовать API панели действий для создания интерфейса пользователя с вкладками в приложении Xamarin.Android.
ms.prod: xamarin
ms.assetid: B7E60AAF-BDA5-4305-9000-675F0438734D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/06/2018
ms.openlocfilehash: 3e96ce2064391d585943f4d79453f8b4f8c6f583
ms.sourcegitcommit: 1561c8022c3585655229a869d9ef3510bf83f00a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/27/2018
ms.locfileid: "32020131"
---
# <a name="tabbed-layouts-with-the-actionbar"></a>Макеты с вкладками с панели действий

_В этом руководстве представлены и объясняется, как использовать API панели действий для создания интерфейса пользователя с вкладками в приложении Xamarin.Android._


## <a name="overview"></a>Обзор

Панель действий — это шаблон Android пользовательского интерфейса, который используется для предоставления согласованного пользовательского интерфейса для важные возможности, как вкладки, удостоверение приложения, меню и поиска. В Android (API уровня 11) версии 3.0 Google появился API-интерфейсы панели действий для платформы Android. API-интерфейсы панели действий вызвать темы пользовательского интерфейса для обеспечения согласованного внешнего вида и поведения и классы, позволяющие с вкладками пользовательских интерфейсов. В этом руководстве описывается добавление приложения Xamarin.Android вкладок панели действий. Также описывается использование v7 библиотеку поддержки Android для вкладки панели действий backport Xamarin.Android приложения, предназначенные для Android 2.1 на Android 2.3. 

Обратите внимание, что `Toolbar` более новой версии и более обобщенное действие панель компонент, который следует использовать вместо объекта `ActionBar` (`Toolbar` был предназначен для замены `ActionBar`). Дополнительные сведения см. в разделе [инструментов](~/android/user-interface/controls/tool-bar/index.md). 



## <a name="requirements"></a>Требования

Xamarin.Android любое приложение, предназначенное для API уровня 11 (Android 3.0) или более поздней версии имеет доступ к API-интерфейсы панели действий в рамках собственных API Android. 

Некоторые API, панели действий перенесенных обратно в API уровня 7 (Android 2.1) и доступны через [V7 AppCompat библиотеки](http://developer.android.com/tools/support-library/features.html#v7-appcompat), который становится доступным для приложений Xamarin.Android через [Xamarin Android библиотека поддержки - V7 ](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) пакета.



## <a name="introducing-tabs-in-the-actionbar"></a>Общие сведения о вкладках в панели действий

Панели действий пытается одновременно отображаться все его вкладки и сделать все вкладки одинакового размера, исходя из ширины самого широкого метку вкладки. Это показано на следующем снимке экрана: 

![Снимок экрана: пример из панели действий с все вкладки одинакового размера показано](with-action-bar-images/image1.png)

Панели действий не может отобразить все вкладки, настроите вкладки в представлении, поддерживающим горизонтальную прокрутку. Пользователь может проведите влево или вправо для просмотра остальных вкладках. На этом снимке экрана из Google Play показан пример этого. 

![Снимок экрана примера вкладок, поддерживающим горизонтальную прокрутку в представлении в виде](with-action-bar-images/image2.png)

Каждая вкладка в области действия должен быть связан с [ *фрагмент*](~/android/platform/fragments/index.md). При выборе вкладки, фрагмент, который связан с вкладке будет отображаться в приложении. Панели действий не отвечает за отображение соответствующего фрагмента для пользователя. Вместо этого панели действий будет уведомлять об изменениях состояния на вкладке через класс, реализующий интерфейс ActionBar.ITabListener приложения. Этот интерфейс предоставляет три метода обратного вызова, которые Android будет вызывать при изменении состояния вкладки: 

-  **OnTabSelected** -этот метод вызывается, когда пользователь выбирает вкладке. Должно появиться фрагмента.

-  **OnTabReselected** -этот метод вызывается, когда вкладке уже установлен, но повторном выборе пользователем. Этот обратный вызов обычно используется для обновления или обновления отображаемого фрагмента.

-  **OnTabUnselected** -этот метод вызывается, когда пользователь выбирает другую вкладку. Этот обратный вызов используется для сохранения состояния в отображаемых фрагмент, прежде чем он исчезнет.

Создает оболочку для Xamarin.Android `ActionBar.ITabListener` с событиями на `ActionBar.Tab` класса. Приложения может назначить обработчики событий для одного или нескольких таких событий. Существует три события (один для каждого метода в `ActionBar.ITabListener`) вызовет вкладки панели действий: 

-  TabSelected
-  TabReselected
-  TabUnselected



### <a name="adding-tabs-to-the-actionbar"></a>Добавление вкладок в панели действий

Панели действий собственного Android (API уровня 11) версии 3.0 и более поздней версии и доступна для любого приложения Xamarin.Android, как минимум этот API предназначен. 

Ниже показано, как добавить вкладки панели действий в Android действие: 

1. В `OnCreate` метод действия &ndash; *перед инициализацией все графические элементы пользовательского интерфейса* &ndash; приложение должно установить `NavigationMode` на `ActionBar` для `ActionBar.NavigationModeTabs` как показано в этом коде фрагмент кода:

   ```csharp
   ActionBar.NavigationMode = ActionBarNavigationMode.Tabs;
   SetContentView(Resource.Layout.Main);
   ```

2. Создать новую вкладку, используя `ActionBar.NewTab()`.

3. Назначение обработчиков событий или предоставить пользовательский `ActionBar.ITabListener` реализацию, которая будет реагировать на события, возникающие, когда пользователь взаимодействует с вкладками панели действий.

4. Добавить вкладку, которая была создана на предыдущем шаге, чтобы `ActionBar`.


Ниже приведен один пример использования эти шаги для добавления вкладки для приложения, использующего обработчики событий для реагирования на изменения состояния. 

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


#### <a name="event-handlers-vs-actionbaritablistener"></a>Обработчики событий vs ActionBar.ITabListener

Приложения должны использовать обработчики событий и `ActionBar.ITabListener` для различных сценариев. Обработчики событий также имеют определенное количество синтаксического удобства; сохраняют вас от необходимости создания класса и реализовать `ActionBar.ITabListener`. Удобство есть своя цена &ndash; Xamarin.Android выполняет это преобразование автоматически, создание одного класса и реализация `ActionBar.ITabListener` для вас. Это нормально, если приложение имеет ограниченное число вкладок. 

При работе с слишком большого числа вкладок или совместного использования общей функциональности между вкладками на панели действий, это может быть эффективными в плане использования памяти и производительности, чтобы создать пользовательский класс, который реализует `ActionBar.ITabListener`и совместного использования одного экземпляра класса. Это уменьшит количество GREF которая используется приложением Xamarin.Android. 



### <a name="backwards-compatibility-for-older-devices"></a>Обратная совместимость для старых устройств

[Библиотеку поддержки Android версии 7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/) назад порты вкладок панели действий, чтобы Android 2.1 (API уровня 7). Вкладки доступны в приложении Xamarin.Android после добавления этого компонента в проект.

Чтобы использовать панели действий, действия должен подкласс `ActionBarActivity` и используйте AppCompat темы, как показано в следующем фрагменте кода:

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/Theme.AppCompat", MainLauncher = true, Icon = "@drawable/ic_launcher")]
public class MainActivity: ActionBarActivity
```

Действие может получить ссылку на его панели действий из `ActionBarActivity.SupportingActionBar` свойство. В следующем фрагменте кода показан пример того, панели действий в действии:

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

В этом руководстве мы обсуждали для создания интерфейса пользователя с вкладками в Xamarin.Android, с помощью панели действий. Мы узнали, как добавить вкладки в панели действий и способы взаимодействия с вкладки событий с помощью действия `ActionBar.ITabListener` интерфейса. Также мы узнали, как вкладки библиотеку поддержки Android версии 7 AppCompat пакета backports панели действий в более старых версиях Android. 


## <a name="related-links"></a>Связанные ссылки

- [ActionBarTabs (пример)](https://developer.xamarin.com/samples/monodroid/UserInterface/ActionBarTabs/)
- [ToolBar](~/android/user-interface/controls/tool-bar/index.md)
- [Фрагменты](~/android/platform/fragments/index.md)
- [ActionBar](http://developer.android.com/guide/topics/ui/actionbar.html)
- [ActionBarActivity](http://developer.android.com/reference/android/support/v7/app/ActionBarActivity.html)
- [Шаблон панели действий](http://developer.android.com/design/patterns/actionbar.html)
- [Android версии 7 совместимости приложений](http://developer.android.com/tools/support-library/features.html#v7-appcompat)
- [Пакет NuGet AppCompat v7 библиотеки поддержки Xamarin.Android](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)
