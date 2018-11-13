---
title: XAML Standard (Предварительная версия)
description: В этой статье объясняется, как начать работу с изучения предварительной версии стандартных XAML в Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 24382DF1-BE70-4608-B86F-B79FB23E4A78
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/15/2017
ms.openlocfilehash: d9fb19fb233c25e5dd525c7c157013ef66f4a4f3
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51562761"
---
# <a name="xaml-standard-preview"></a>XAML Standard (Предварительная версия)

![Предварительный просмотр](~/media/shared/preview.png)

Выполните следующие действия, чтобы поэкспериментировать с помощью стандартного XAML в Xamarin.Forms.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Скачайте [предварительной версии пакета NuGet здесь](https://aka.ms/xf-xamlstandard-nuget).
2. Добавить **Xamarin.Forms.Alias** пакет NuGet в проекты Xamarin.Forms .NET Standard и платформы.
3. Инициализировать пакет с `Alias.Init()`
4. Добавление `xmlns:a` ссылки `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Использовать типы в XAML - см. в разделе [Справочник по элементам управления](controls.md) Дополнительные сведения.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio для Mac](#tab/macos)

1. Скачайте [предварительной версии пакета NuGet здесь](https://aka.ms/xf-xamlstandard-nuget).
2. Добавить **Xamarin.Forms.Alias** пакет NuGet в проекты Xamarin.Forms .NET Standard и платформы.
3. Инициализировать пакет с `Alias.Init()`
4. Добавление `xmlns:a` ссылки `xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"`
5. Использовать типы в XAML - см. в разделе [Справочник по элементам управления](controls.md) Дополнительные сведения.

-----

Следующий XAML демонстрирует некоторые XAML стандартные элементы управления, используемых в Xamarin.Forms `ContentPage`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ContentPage 
    xmlns="http://xamarin.com/schemas/2014/forms" 
    xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" 
    xmlns:a="clr-namespace:Xamarin.Forms.Alias;assembly=Xamarin.Forms.Alias"
    x:Class="XAMLStandardSample.ItemsPage" 
    Title="{Binding Title}" x:Name="BrowseItemsPage">
    <ContentPage.ToolbarItems>
        <ToolbarItem Text="Add" Clicked="AddItem_Clicked" />
    </ContentPage.ToolbarItems>
    <ContentPage.Content>
        <a:StackPanel>
            <ListView x:Name="ItemsListView" ItemsSource="{Binding Items}" VerticalOptions="FillAndExpand" HasUnevenRows="true" RefreshCommand="{Binding LoadItemsCommand}" IsPullToRefreshEnabled="true" IsRefreshing="{Binding IsBusy, Mode=OneWay}" CachingStrategy="RecycleElement" ItemSelected="OnItemSelected">
                <ListView.ItemTemplate>
                    <DataTemplate>
                        <ViewCell>
                            <StackLayout Padding="10">
                                <a:TextBlock Text="{Binding Text}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemTextStyle}" FontSize="16" />
                                <a:TextBlock Text="{Binding Description}" LineBreakMode="NoWrap" Style="{DynamicResource ListItemDetailTextStyle}" FontSize="13" />
                            </StackLayout>
                        </ViewCell>
                    </DataTemplate>
                </ListView.ItemTemplate>
            </ListView>
        </a:StackPanel>
    </ContentPage.Content>
</ContentPage>
```

> [!NOTE]
> Требуя xmlns `a:` префикс имени XAML стандартные элементы управления является ограничением текущей предварительной версии.


## <a name="related-links"></a>Связанные ссылки

- [Предварительная версия NuGet](https://aka.ms/xf-xamlstandard-nuget)
- [Справочник по элементам управления](controls.md)
