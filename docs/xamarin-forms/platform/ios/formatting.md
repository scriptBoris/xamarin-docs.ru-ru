---
title: Добавление форматирование конкретных операций ввода-вывода
description: В этой статье объясняется, как задать внешний вид конкретного iOS без использования пользовательского средства визуализации Xamarin.Forms.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 3b8a440617dedfbe23f869e865b3cedae21d6c5b
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209809"
---
# <a name="adding-ios-specific-formatting"></a>Добавление форматирование конкретных операций ввода-вывода

Один из способов задать iOS конкретных форматирования — создать [пользовательское средство отрисовки](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) для элемента управления и стили специфические для платформы набор и цвета для каждой платформы.

Другие параметры для управления способом, внешний вид приложения iOS Xamarin.Forms включают:

* Настройка отображения параметров в [ **Info.plist**](#info-plist)
* Настройка стилей элемента управления с помощью [ `UIAppearance` API](#uiappearance)

Эти возможности описаны ниже.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Настройка Info.plist

**Info.plist** файл позволяет настраивать некоторые аспекты renderering приложения iOS, такие как способ (и необходимость) отображается в строке состояния.

Например [примере Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/) использует следующий код, чтобы задать цвет и цвет текста на панели навигации на всех платформах:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

Результат показан на приведенном ниже фрагменте кода экрана. Обратите внимание, что элементы строки состояния черный (не может быть установлен в Xamarin.Forms, так как она является компонентом платформы).

![](theme-images/status-default-sml.png "iOS темы")

В идеале в строке состояния, также будет белый — что-то можно выполнить непосредственно в проекте iOS. Добавьте следующие записи для **Info.plist** заставить в строке состояния, чтобы быть добавлены в:

![](theme-images/info-plist.png "операций ввода-вывода записей Info.plist")

или изменение соответствующего **Info.plist** непосредственно включаемых файлов:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Теперь при запуске приложения на панели навигации отображается зеленым цветом, а его текст белого (из-за форматирования Xamarin.Forms) *и* текст строки состояния, также очень белый благодаря конфигурации iOS:

![](theme-images/status-white-sml.png "iOS темы")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

[ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) может использоваться для задания свойства визуальных элементов на многие элементы управления iOS *без* того, чтобы создавать [пользовательское средство отрисовки](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Добавление одной строки кода для **AppDelegate.cs** `FinishedLaunching` метод задать стиль всех элементов управления данного типа с помощью их `Appearance` свойство. Следующий код содержит два примера - глобально стиля на вкладке панели и переключите управления:

**AppDelegate.cs** в проект iOS

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

По умолчанию значок панели выбранной вкладки в [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
будет синей:

![](theme-images/tabbar-default.png "По умолчанию значок панели вкладки в TabbedPage iOS")

Чтобы изменить это поведение, установите `UITabBar.Appearance` свойство:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Это приводит к выбранной вкладки зеленым цветом:

![](theme-images/tabbar-custom.png "Зеленый значок панели вкладки в TabbedPage iOS")

Этот API позволяет настраивать внешний вид Xamarin.Forms `TabbedPage` на iOS с помощью очень небольшого объема кода. Ссылаться на [Настройка вкладки рецепт](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) Дополнительные сведения об использовании пользовательского средства визуализации, чтобы задать определенный шрифт для вкладки.

### <a name="uiswitch"></a>UISwitch

`Switch` Управления приведен еще один пример, который можно легко стили:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Эти снимки экрана два Показать значение по умолчанию `UISwitch` элемента управления в левой части и настроенную версию (параметр `Appearance`) в правой [примере Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "По умолчанию цвет UISwitch") ![](theme-images/switch-custom.png "настроить цвет UISwitch")

### <a name="other-controls"></a>Другие элементы управления

Многие элементы управления пользовательского интерфейса iOS может иметь их по умолчанию цвета и другие атрибуты, заданные с помощью [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).



## <a name="related-links"></a>Связанные ссылки

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Настраивать вкладки](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
