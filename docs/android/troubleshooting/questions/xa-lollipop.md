---
title: Какая версия Xamarin.Android добавлена поддержка без описания операций?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 065c68a373f67bb352b59dc88ef89daec8b51ef8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764473"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>Какая версия Xamarin.Android добавлена поддержка без описания операций?

**Примечание:** изначально данное руководство написано для Android L предварительного просмотра.

-   [Xamarin.Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) добавлена поддержка предварительной версии Android L.
-   [Xamarin.Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) добавлена поддержка Android без описания операций.

Xamarin поддерживает только активно текущей стабильной версии средства Xamarin. Приведенные ниже сведения предоставляются «как-—» для более старых версий средств. Последние сведения о выпусках Xamarin, проверьте [здесь](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>«Отсутствует android.jar для уровень API 21» в режиме предварительного просмотра Android L

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Может появиться следующее сообщение об ошибке (или схожий):

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Это сообщение означает, что Android SDK платформы для API 21 уровень не установлен. Либо установите в диспетчере Android SDK (Сервис > Откройте диспетчер Android SDK Manager...), или изменить Xamarin.Android проект на версию API, установленной.

Существует несколько способа решения этой проблемы:

1. Изменить проект, чтобы нацелить его API 19 или ниже.

2. Переименуйте папку android 21 от android 21 для android L. (В лучшем случае это следует использовать только в качестве временного исправление, и он может не работать очень хорошо.)

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Временно перейти обратно на предварительной версии Android уровень API 21 символа «L» [1]:

    1.  Удалить **% LOCALAPPDATA %\\Android\\пакета sdk android\\платформы\\android 21** 
    2.  Извлечения [1] на **C:\\пользователей\\<username>\\AppData\\локального\\Android\\пакета sdk android\\платформы** для создания **android-L** папки.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio для Mac](#tab/vsmac)

Может появиться следующее сообщение об ошибке (или схожий):

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Это означает, что Android SDK платформы для API 21 уровень не установлен. Либо установки Android SDK Manager (Сервис > Диспетчер пакетов SDK...), или изменить Xamarin.Android проект на версию API, установленного.

Существует несколько способа решения этой проблемы:

1. Изменить проект, чтобы нацелить его API 19 или ниже.

2. Переименуйте папку android 21 от android 21 для android L. (В лучшем случае это следует использовать только в качестве временного исправление, и он может не работать очень хорошо.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Временно перейти обратно на предварительной версии Android уровень API 21 символа «L» [1]:

    1.  Удалить **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Извлечения [1] на **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** для создания **android-L** папки.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
