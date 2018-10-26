---
title: Обеспечение обратной совместимости с пакетом поддержки Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: 18dac665eec1c5d3ac64065c37e73022670c1ba5
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108851"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Обеспечение обратной совместимости с пакетом поддержки Android

Полезность фрагментов будет ограничено без обеспечения обратной совместимости с версией Android 3.0 устройствами (уровень API 11). Чтобы предоставить эту возможность, Google ввела [библиотека поддержки](http://developer.android.com/sdk/compatibility-library.html) (изначально назывался *библиотеки Android совместимости* когда был выпущен) какие бэкпортированным некоторые интерфейсы API из более новых версиях Android для более старых версиях Android. Это Android пакет поддержки, который позволяет устройствам под управлением Android версии 1.6 (API уровня 4) для Android 2.3.3. (Уровень API 10).

> [!NOTE]
> Только `ListFragment` и `DialogFragment` можно загрузить с помощью Android пакет поддержки. Ни с одним другим Fragment подклассов, такие как `PreferenceFragment,` поддерживаются в Android пакет поддержки. Они не будут работать в приложениях с версией Android 3.0. 


## <a name="adding-the-support-package"></a>Добавление пакета поддержки

Пакет поддержки Android не добавляется автоматически в приложение Xamarin.Android. Xamarin предоставляет [пакет NuGet библиотеки Android поддерживают v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) для упрощения процедуры добавления библиотеки поддержки в приложение Xamarin.Android. Включить пакеты поддержки в вашей Xamarin.Android приложение включать [v4 библиотеки Android поддерживают](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) компонент в проект Xamarin.Android, как показано на следующем снимке экрана: 

[![Снимок экрана для поддержки библиотеки Android v4 пакет, добавляемый в проект](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

После выполнения этих действий, становится возможным использовать фрагменты в более ранних версиях Android. API-интерфейсы фрагмент будет работать же теперь в более ранних версиях, за исключением следующих случаев: 

-   **Изменить минимальную версию Android** &ndash; приложению больше не нужно применять Android 3.0 или более поздней версии, в том случае, как показано ниже: 

    [![Снимок экрана Android по минимум целевой, задаваемое в разделе манифеста Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **Расширить FragmentActivity** &ndash; действий, которые размещаются фрагментов теперь должен наследовать от `Android.Support.V4.App.FragmentActivity` , а не из `Android.App.Activity` . 

-   **Обновление пространства имен** &ndash; классы, наследующие от `Android.App.Fragment` теперь должен наследовать от `Android.Support.V4.App.Fragment` . Удалить с помощью оператора " `using Android.App;` " в верхней части исходного кода файл и замените ее строкой " `using Android.Support.V4.App` «. 

-   **Используйте SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` предоставляет `SupportingFragmentManager` свойство, которое необходимо использовать для получения ссылки на `FragmentManager` . Пример: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

С изменениями его можно будет запустить приложение на основе фрагмента на Android 1.6 или 2.x, а также на Honeycomb и Ice Cream Sandwich. 


## <a name="related-links"></a>Связанные ссылки

- [NuGet v4 библиотеки поддержки Android](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
