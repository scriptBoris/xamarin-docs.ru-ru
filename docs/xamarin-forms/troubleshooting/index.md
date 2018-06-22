---
title: Устранение неполадок
description: Распространенные ошибки и способах их устранения
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63291951-7375-4CBF-BCC3-2E4AD157A2C8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fbe4fb6fce52636b59a9637ee0150c4c19fcc9da
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30788527"
---
# <a name="troubleshooting"></a>Устранение неполадок

_Распространенные ошибки и способах их устранения_

## <a name="error-unable-to-find-a-version-of-xamarinforms-compatible-with"></a>Ошибка: «не удалось найти версию Xamarin.Forms совместим с...»

Следующие ошибки могут появиться в **консоли пакета** окна при обновлении всех пакетов Nuget в Xamarin.Forms решение или проект приложения Xamarin.Forms Android:

```csharp
Attempting to resolve dependency 'Xamarin.Android.Support.v7.AppCompat (= 23.3.0.0)'.
Attempting to resolve dependency 'Xamarin.Android.Support.v4 (= 23.3.0.0)'.
Looking for updates for 'Xamarin.Android.Support.v7.MediaRouter'...
Updating 'Xamarin.Android.Support.v7.MediaRouter' from version '23.3.0.0' to '23.3.1.0' in project 'Todo.Droid'.
Updating 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0' to 'Xamarin.Android.Support.v7.MediaRouter 23.3.1.0' failed.
Unable to find a version of 'Xamarin.Forms' that is compatible with 'Xamarin.Android.Support.v7.MediaRouter 23.3.0.0'.
```

### <a name="what-causes-this-error"></a>В чем причина этой ошибки?

Visual Studio для Mac (или Visual Studio) может означать, что обновления доступны для Xamarin.Forms Nuget packge *и все его зависимости*. В Xamarin Studio, решения в **пакетов** узел может выглядеть следующим образом (номера версий может отличаться):

![](images/updates-available.png "Папка пакетов проекта Android")

Эта ошибка может возникнуть при попытке обновить _все_ пакеты.

Это так, как с Android проектов до версии целевой/компиляции Android 6.0 (API 23) или ниже, Xamarin.Forms жесткие зависимости *конкретных* версии Android, поддерживают пакетов. Несмотря на то, что обновленные версии эти пакеты могут быть доступны, Xamarin.Forms не быть несовместимым с ними.

В этом случае необходимо обновить _только_ **Xamarin.Forms** пакета, как это позволит гарантировать, что зависимости остаются на совместимых версий. Другие пакеты, которые были добавлены в проект может также обновляться по отдельности до тех пор, пока они не вызывают пакеты поддержка Android для обновления.


> [!NOTE]
> Если вы используете Xamarin.Forms 2.3.4 или более поздней версии **и** задана версия целевой/компиляции проекта Android, до версии 7.0, Android (API 24) или более поздней версии, а затем жестких зависимостей, упомянутых выше, больше не применяются и обновлении поддержки пакеты, независимо от пакета Xamarin.Forms.


### <a name="fix-remove-all-packages-and-re-add-xamarinforms"></a>Исправление: Удалите все пакеты, а затем добавьте Xamarin.Forms

Если **Xamarin.Android.Support** пакеты были обновлены, чтобы несовместимые версии, простейшим решением является:

1. Затем вручную удалите все пакеты Nuget в проект Android,
2. Снова добавьте **Xamarin.Forms** пакета.

Это будет автоматически загружать *правильный* версии других пакетов.

Для просмотра видео этого процесса, ссылаться на это [форумы post](https://forums.xamarin.com/discussion/comment/170012/#Comment_170012).
