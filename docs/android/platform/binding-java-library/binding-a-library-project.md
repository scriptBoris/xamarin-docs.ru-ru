---
title: Привязка проекта библиотеки Eclipse
description: В этом пошаговом руководстве объясняется, как использовать шаблоны проектов Xamarin.Android для привязки проекта библиотеки Eclipse Android.
ms.prod: xamarin
ms.assetid: CEE90F8A-164B-4155-813A-7537A665A7E7
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a5887748eda8af89b9215e3e121db592dfa73935
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50116606"
---
# <a name="binding-an-eclipse-library-project"></a>Привязка проекта библиотеки Eclipse

_В этом пошаговом руководстве объясняется, как использовать шаблоны проектов Xamarin.Android для привязки проекта библиотеки Eclipse Android._


## <a name="overview"></a>Обзор

Несмотря на то что. Все чаще становятся нормой для библиотеки Android распространения aar-файлы, в некоторых случаях бывает необходимо создать привязку для *проект библиотеки Android*. Проекты библиотеки Android — это специальные проекты Android, которые содержат совместного использования кода и ресурсов, которые могут ссылаться проекты приложения Android. Как правило привязывается к проект библиотеки Android при создании библиотеки в интегрированной среде разработки Eclipse.
В этом пошаговом руководстве приведены примеры того, как создать проект библиотеки Android. ZIP из структуры каталогов проекта Eclipse.

Проекты библиотеки Android отличаются от обычных проектах Android тем, что они не компилируются в пакет APK, а не сами по себе, развертываемые на устройстве. Вместо этого на которые ссылается проект приложения Android предназначен проект библиотеки Android. При построении проекта Android приложения сначала компиляции проекта библиотеки Android. Проект приложения Android будет затем которого поглощается в проект скомпилированную библиотеку Android и включить код и ресурсы в пакет APK для распространения. Из-за этого отличия Создание привязки для проекта библиотеки Android немного отличается от создания привязки для Java. JAR-ФАЙЛ или. AAR-файла.



## <a name="walkthrough"></a>Пошаговое руководство

Чтобы использовать проект библиотеки Android в проекте Xamarin.Android привязки Java лучше сначала необходимо создать проект библиотеки Android в Eclipse. На следующем рисунке показан пример одного проекта библиотеки Android после компиляции: 

[![Пример проекта библиотеки, в Eclipse](binding-a-library-project-images/build-lib-in-eclipse.png)](binding-a-library-project-images/build-lib-in-eclipse.png#lightbox)

Обратите внимание на то, что временный компиляции исходного кода из проекта библиотеки Android. JAR-файл с именем **android mapviewballoons.jar**, и что ресурсы были скопированы **bin/res/обработайте** папки. 

После компиляции проекта библиотеки Android в Eclipse, его можно затем привязать с помощью проекта Xamarin.Android привязки Java. Первый. Создаются ZIP-файл, который содержит **bin** и **res** папки проекта библиотеки Android. Важно удалить промежуточными **перемалывания** подкаталог, чтобы ресурсы находятся в **bin/res**. На следующем рисунке показан содержимое одного такого. ZIP-файл: 

[![Содержимое ZIP-файл проекта библиотеки Android](binding-a-library-project-images/contents-of-zip-file.png)](binding-a-library-project-images/contents-of-zip-file.png#lightbox)

Это. ZIP-файл добавляется в проект Xamarin.Android Java привязки, как показано на следующем снимке экрана:

[![ZIP-добавить проект привязки Java](binding-a-library-project-images/zip-in-binding-project.png)](binding-a-library-project-images/zip-in-binding-project.png#lightbox)

Обратите внимание, что действие построения. ZIP-файл было автоматически присвоено **LibraryProjectZip**.

Если таковые имеются. JAR-файлы, которые требуются в проекте библиотеки Android, они должны быть добавлены **Jars** папки проекта привязка библиотеки Java и **действие при построении** присвоено **ReferenceJar**. Пример этого можно увидеть на следующем снимке экрана: 

[![Значение ReferenceJar действие построения](binding-a-library-project-images/set-to-referencejar.png)](binding-a-library-project-images/set-to-referencejar.png#lightbox)

После выполнения этих действий, проект Xamarin.Android привязки Java можно использовать, как описано ранее в этом документе.

> [!NOTE]
> Компиляция проектов библиотек Android в других средах IDE в настоящее время не поддерживается. Другие интегрированные среды разработки не может создать структуру каталогов или файлов в **bin** папке, что и Eclipse. 


## <a name="summary"></a>Сводка

В этой статье мы рассмотрели процесс привязки проект библиотеки Android. Мы создали проект библиотеки Android в Eclipse, а затем мы создали ZIP-файл из **bin** и **res** папки проекта библиотеки Android. Затем мы использовали этот zip для создания проекта Xamarin.Android привязки Java. 

