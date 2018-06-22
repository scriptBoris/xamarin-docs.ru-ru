---
title: Можно обновить шаблон по умолчанию Xamarin.Forms новой пакет NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: fc479b4b0651e3312b855673730be21c2076d833
ms.sourcegitcommit: b0a1c3969ab2a7b7fe961f4f470d1aa57b1ff2c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/10/2018
ms.locfileid: "34049838"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>Можно обновить шаблон по умолчанию Xamarin.Forms новой пакет NuGet?

В этом руководстве в качестве примера используется шаблон Стандартная .NET Xamarin.Forms библиотеки, но один и тот же общий метод будет работать для Xamarin.Forms общий проект шаблона. Это руководство предназначено в примере из Xamarin.Forms 2.1.0.6529 1.5.1.6471 для обновления, но те же действия можно вместо этого задать другие версии по умолчанию.

1.  Скопируйте исходный шаблон `.zip` из:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2.  Распакуйте `.zip` во временную папку.

3.  Измените все вхождения старая версия пакета форм в новую версию, которую вы хотите использовать.
    *   `FormsTemplate\FormsTemplate.vstemplate`
    *   `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    *   `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Пример: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4.  Измените элемент «name» основных [файл многопроектного шаблона](http://msdn.microsoft.com/library/ms185308.aspx) (`Xamarin.Forms.PCL.vstemplate`), чтобы сделать его уникальным. Пример:
    > <Name>Пустое приложение (переносимое Xamarin.Forms) - 2.1.0.6529</Name>

5.  Повторно заархивировать папку весь шаблон. Убедитесь в том соответствовать структуре исходный файл `.zip` файла. `Xamarin.Forms.PCL.vstemplate` Файл должен быть в верхней части `.zip` файла не внутри папки.

6.  Создайте подкаталог «Приложений для мобильных устройств» в папке шаблонов Visual Studio на пользователя:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7.  Скопируйте новой папки шаблона ZIP-вверх в новый каталог «Мобильные приложения».

8.  Загрузите пакет NuGet, которая соответствует версии на шаге 3. Например [ http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529 ](http://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (см. также [ http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file ](http://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) и скопируйте его в соответствующую вложенную папку в папке расширения Xamarin для Visual Studio:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
