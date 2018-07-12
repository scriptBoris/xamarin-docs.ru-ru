---
title: Тема свет Xamarin.Forms
description: В этой статье объясняется, как использовать тему свет Xamarin.Forms в приложении.
ms.prod: xamarin
ms.assetid: D5D16AE3-F51F-4359-B37A-E1087ECE512B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: 7f40e375d653acec60f8848627234ab46fcce8de
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38842756"
---
# <a name="xamarinforms-light-theme"></a>Тема свет Xamarin.Forms

![](~/media/shared/preview.png "Этот API доступна в предварительной версии")

> [!NOTE]
> Темы требуется предварительной версии Xamarin.Forms 2.3. Проверьте [советы по устранению неполадок](~/xamarin-forms/user-interface/themes/index.md) при возникновении ошибок.

Для использования темы света:

## <a name="1-add-nuget-packages"></a>1. Добавьте пакеты Nuget

* Xamarin.Forms.Theme.Base
* Xamarin.Forms.Theme.Light

## <a name="2-add-to-the-resource-dictionary"></a>2. Добавить в словарь ресурсов

В **App.xaml** файла добавьте новый пользовательский `xmlns` для темы, а затем убедитесь, ресурсы темы объединяются со словарем ресурсов приложения.
Ниже приведен пример файла XAML:

```xaml
<?xml version="1.0" encoding="utf-8"?>
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="EvolveApp.App"
             xmlns:light="clr-namespace:Xamarin.Forms.Themes;assembly=Xamarin.Forms.Theme.Light">
    <Application.Resources>
        <ResourceDictionary MergedWith="light:LightThemeResources" />
    </Application.Resources>
</Application>
```

## <a name="3-load-theme-classes"></a>3. Загрузка классов тем

Выполните это [этап устранения неполадок](~/xamarin-forms/user-interface/themes/index.md) и добавьте необходимый код в проектах приложений Android и iOS.

## <a name="4-use-styleclass"></a>4. Использовать StyleClass

Ниже приведен пример кнопки и метки "светлой" темой, а также разметку, которая создает их.

[![](light-images/light-theme-sml.png "Кнопки и метки \"светлой\" темой")](light-images/light-theme.png#lightbox "кнопки и метки \"светлой\" темой")

```xaml
<StackLayout Padding="20">
    <Button Text="Button Default" />
    <Button Text="Button Class Default" StyleClass="Default" />
    <Button Text="Button Class Primary" StyleClass="Primary" />
    <Button Text="Button Class Success" StyleClass="Success" />
    <Button Text="Button Class Info" StyleClass="Info" />
    <Button Text="Button Class Warning" StyleClass="Warning" />
    <Button Text="Button Class Danger" StyleClass="Danger" />
    <Button Text="Button Class Link" StyleClass="Link" />
    <Button Text="Button Class Default Small" StyleClass="Small" />
    <Button Text="Button Class Default Large" StyleClass="Large" />
</StackLayout>
```

[Полный список встроенных классов](~/xamarin-forms/user-interface/themes/index.md) показывает, какие стили доступны для некоторых общих элементов управления.
