---
title: Устранение ошибок установки библиотеки
description: В некоторых случаях могут возникнуть ошибки при установке библиотеки поддержки Android. Это руководство содержит обходные пути для некоторых распространенных ошибок.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2AE68ACE-8496-445D-BF17-5E4097D4AE35
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/14/2018
ms.openlocfilehash: 2ef81cfda92a6497e69f27b0584a97996094b1a4
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50107134"
---
# <a name="resolving-library-installation-errors"></a>Устранение ошибок установки библиотеки

_В некоторых случаях могут возникнуть ошибки при установке библиотеки поддержки Android. Это руководство содержит обходные пути для некоторых распространенных ошибок._

## <a name="overview"></a>Обзор

При построении проекта приложения Xamarin.Android, могут возникнуть ошибки сборки, когда Visual Studio или Visual Studio для Mac пытаются загрузить и установить зависимости библиотек. Многие из этих ошибок возникают проблемы с сетевым подключением, повреждения файлов или проблемы управления версиями. В этом руководстве описаны наиболее распространенные ошибки установки библиотеки поддержки и описывается, как обойти эти проблемы и получите свой проект приложения, повторной сборкой. 

 
 
## <a name="errors-while-downloading-m2repository"></a>Во время загрузки m2Repository

Может появиться **m2repository** ошибки при ссылке на пакет NuGet служб Android библиотек поддержки или Google Play. Сообщение об ошибке выглядит следующим образом:

```shell
Download failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\22.2.1\content directory.
```

Этот пример предназначен для **android\_m2repository\_r16**, но вам могут встречаться это же сообщение об ошибке для различных версий, такие как **android\_m2repository\_r18**  или **android\_m2repository\_r25**. 



### <a name="automatic-recovery-from-m2repository-errors"></a>Автоматическое восстановление после ошибок m2repository 

Часто эту проблему можно исправить, удалить проблемный библиотеку и перестроение в соответствии с следующие действия: 

1. Перейдите в каталог библиотеки поддержки на вашем компьютере:

    -   В Windows, поддержка библиотек расположены в **C:\\пользователей\\_username_\\AppData\\локального\\Xamarin**. 

    -   В Mac OS X, поддержка библиотек расположены в **/Users/_username_/.local/share/Xamarin**. 

2. Найдите папку библиотеки и версии, соответствующее сообщение об ошибке. Например, находится в папке библиотеки и версии выше сообщение об ошибке **Android.Support.v4\\22.2.1**:

    [![Пример расположение папки для 22.2.1 библиотеку поддержки](resolving-library-installation-errors-images/01-example-location.png)](resolving-library-installation-errors-images/01-example-location.png#lightbox)

3. Удалите содержимое папки версии. Не забудьте удалить **ZIP-файл** файл, а также **содержимого** и **embedded** подкаталогам в этой папке. Пример сообщения об ошибке, показанный выше, файлы и подкаталоги, показано на следующем снимке экрана (**содержимого**, **embedded**, и **android_m2repository_r16.zip**), чтобы удалены:

    [![Пример содержимого 22.2.1 поддержки папки библиотеки](resolving-library-installation-errors-images/02-example-folder-vs.png)](resolving-library-installation-errors-images/02-example-folder-vs.png#lightbox)

   Обратите внимание, что важно удалить *всей* содержимое этой папки. Несмотря на то, что эта папка может содержать «отсутствует» **android\_m2repository\_r16.zip** файл, этот файл был частично Скачанный или поврежден.

4. Перестройте проект &ndash; это вызовет процессе сборки, чтобы повторно скачать отсутствующие библиотеку.

В большинстве случаев эти шаги позволяют устранить ошибку сборки и вы сможете продолжить. При удалении библиотеки не приводит к устранению этой ошибки сборки, необходимо вручную загрузить и установить **android\_m2repository\_r_nn_.zip** файл, как описано в следующем разделе. 



### <a name="manually-downloading-m2repository"></a>Вручную загружать m2repository

Если вы попытались использовать описанные выше действия автоматического восстановления и по-прежнему имеются ошибки сборки, можно вручную загрузить **android\_m2repository\_r_nn_.zip** файл (с помощью веб-браузер) и установите его в соответствии с следующие действия. Эта процедура также полезна, если у вас нет доступа к Интернету на компьютере разработчика, но существует возможность загрузить архив на другом компьютере. 

1.  Скачайте **android\_m2repository\_r_nn_.zip** файла, соответствующее сообщение об ошибке &ndash; ссылки приведены в следующем списке (вместе с соответствующей MD5-хэш каждого соединения URL-АДРЕС):

    -   [Android\_m2repository\_r33.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r33.zip) &ndash; 5FB756A25962361D17BBE99C3B3FCC44

    -   [Android\_m2repository\_r32.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r32.zip) &ndash; F16A3455987DBAE5783F058F19F7FCDF

    -   [Android\_m2repository\_r31.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r31.zip) &ndash; 99A8907CE2324316E754A95E4C2D786E

    -   [Android\_m2repository\_r30.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r30.zip) &ndash; 05AD180B8BDC7C21D6BCB94DDE7F2C8F

    -   [Android\_m2repository\_r29.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r29.zip) &ndash; 2A3A8A6D6826EF6CC653030E7D695C41

    -   [Android\_m2repository\_r28.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r28.zip) &ndash; 17BE247580748F1EDB72E9F374AA0223

    -   [Android\_m2repository\_r27.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r27.zip) &ndash; C9FD4FCD69D7D12B1D9DF076B7BE4E1C

    -   [Android\_m2repository\_r26.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r26.zip) &ndash; 8157FC1C311BB36420C1D8992AF54A4D

    -   [Android\_m2repository\_r25.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip) &ndash; 0B3F1796C97C707339FB13AE8507AF50

    -   [Android\_m2repository\_r24.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r24.zip) &ndash; 8E3C9EC713781EDFE1EFBC5974136BEA

    -   [Android\_m2repository\_r23.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r23.zip) &ndash; D5BB66B3640FD9B9C6362C9DB5AB0FE7

    -   [Android\_m2repository\_r22.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r22.zip) &ndash; 96659D653BDE0FAEDB818170891F2BB0

    -   [Android\_m2repository\_r21.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r21.zip) &ndash; CD3223F2EFE068A26682B9E9C4B6FBB5

    -   [Android\_m2repository\_r20.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r20.zip) &ndash; 650E58DF02DB1A832386FA4A2DE46B1A

    -   [Android\_m2repository\_r19.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r19.zip) &ndash; 263B062D6EFAA8AEE39E9460B8A5851A

    -   [Android\_m2repository\_r18.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r18.zip) &ndash; 25947AD38DCB4865ABEB61522FAFDA0E

    -   [Android\_m2repository\_r17.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r17.zip) &ndash; 49054774F44AE5F35A6BA9D3C117EFD8

    -   [Android\_m2repository\_r16.zip](https://dl-ssl.google.com/android/repository/android_m2repository_r16.zip) &ndash; 0595E577D19D31708195A83087881EE6

    Если **m2repository** архива не показано в следующей таблице, можно создать URL-адрес загрузки, добавляя в начало **https://dl-ssl.google.com/android/repository/** имя **m2repository** для загрузки. Например, использовать **https://dl-ssl.google.com/android/repository/android\_m2repository\_r10.zip** для загрузки **android\_m2repository\_r10.zip**.

2.  Переименуйте файл в соответствующий хэш MD5, URL-адреса загрузки как показано в приведенной выше таблице. Например, если вы загрузили **android\_m2repository\_r25.zip**, переименуйте его в **0B3F1796C97C707339FB13AE8507AF50.zip**. Если хэш MD5 для URL-адрес загрузки загруженного файла не содержится в таблице, можно использовать [online генератор MD5](http://www.webconfs.com/online-md5-generator.php) для преобразования URL-адрес в строку хэш MD5. 

3.  Скопируйте файл Xamarin **zips** папку: 

    -   В Windows, эта папка находится в **C:\\пользователей\\***username***\\AppData\\локального\\Xamarin\\zips**. 

    -   В Mac OS X, эта папка находится в **/Users/***username***/.local/share/Xamarin/zips**. 

    Например, в следующем снимке экрана показан результат при **android\_m2repository\_r16.zip** загружается и переименован в хэш MD5 его URL-адрес загрузки на Windows:

    [![Пример репозитория r16.zip переименовываемого для 0595E577D19D31708195A83087881EE6.zip](resolving-library-installation-errors-images/03-md5-rename-vs.png)](resolving-library-installation-errors-images/03-md5-rename-vs.png#lightbox)


Эта процедура не устранить ошибку сборки, необходимо вручную загрузить **android\_m2repository\_r_nn_.zip** файл, распакуйте его и установите его содержимое, как описано в следующем разделе. 


### <a name="manually-downloading-and-installing-m2repository-files"></a>Вручную загрузить и установить файлы m2repository

Полностью ручной процесс настройки для восстановления после **m2repository** влечет за собой ошибки загрузки **android\_m2repository\_r_nn_.zip** файл (с помощью веб-браузер), распаковка он и копирование его содержимое в каталог библиотеки поддержки на вашем компьютере. В следующем примере мы будем восстановиться это сообщение об ошибке: 

```shell
Unzipping failed. Please download https://dl-ssl.google.com/android/repository/android_m2repository_r25.zip and extract it to the C:\Users\mgm\AppData\Local\Xamarin\Android.Support.v4\23.1.1\content directory.
```

Следуйте инструкциям ниже, чтобы скачать **m2repository** и установите его содержимое:

1.  Удалите содержимое папки библиотеки, соответствующее сообщение об ошибке. К примеру, в сообщении об ошибке выше следует удалить содержимое **C:\\пользователей\\***username***\\AppData\\локального\\Xamarin\\ Android.Support.v4\\23.1.1.0**. 
    Как уже упоминалось, необходимо удалить все содержимое этого каталога:

    [![Удаление содержимого, внедренными и папки android_m2repository с 23.1.1.0 папки](resolving-library-installation-errors-images/04-delete-contents-vs.png)](resolving-library-installation-errors-images/04-delete-contents-vs.png#lightbox)

2.  Скачайте **android\_m2repository\_r_nn_.zip** файла из Google, которая соответствует ошибке сообщения (см. в таблице в предыдущем разделе для ссылок).

3.  Извлекать этот ключ **ZIP-файл** архива в любом месте (например, на рабочем столе). Это следует создать каталог, соответствующий имени **ZIP-файл** архива. В этом каталоге найдите подкаталог с именем **m2repository**: 

    [![найти в папке m2repository извлечь ZIP-архив](resolving-library-installation-errors-images/05-m2repository-vs.png)](resolving-library-installation-errors-images/05-m2repository-vs.png#lightbox)

4.  В каталоге библиотек с управлением версиями, который удален на шаге 1, повторно создать **содержимого** и **embedded** подкаталоги. Например, следующем снимке экрана показана **содержимого** и **embedded** подкаталогов, создаваемых в **23.1.1.0** папку для **android \_m2repository\_r25.zip**: 

    [![Создание содержимого и папок, внедренные в 23.1.1.0 папки](resolving-library-installation-errors-images/06-recreate-folders-vs.png)](resolving-library-installation-errors-images/06-recreate-folders-vs.png#lightbox)

5.  Копировать **m2repository** из извлеченной **ZIP-файл** в **содержимого** каталог, созданный на предыдущем шаге: 

    [![Снимок экрана m2repository копируется в папку 23.1.1.0/content](resolving-library-installation-errors-images/07-copied-m2repository-vs.png)](resolving-library-installation-errors-images/07-copied-m2repository-vs.png#lightbox)

6.  В извлеченной **ZIP-файл** directory, перейдите к **m2repository\\com\\android\\поддерживает\\поддержки v4** и откройте папку соответствующего номер версии созданной ранее (в этом примере **23.1.1**):

    [![Пример списка файлов, содержащихся в папке support-v4/23.1.1](resolving-library-installation-errors-images/08-zip-contents-vs.png)](resolving-library-installation-errors-images/08-zip-contents-vs.png#lightbox)

7.  Скопируйте все файлы в эту папку, чтобы **embedded** каталог, созданный на шаге 4:

    [![Пример того, файлы копируются в папку 23.1.1.0/embedded](resolving-library-installation-errors-images/09-copied-vs.png)](resolving-library-installation-errors-images/09-copied-vs.png#lightbox)

8.  Убедитесь, что все файлы копируются. **Embedded** directory теперь должен содержать файлы например **.jar**, **.aar**, и **.pom**.

9.  Распакуйте содержимое любой извлеченных **.aar** файлы **embedded** каталога. В Windows, добавьте **ZIP-файл** расширение **.aar** файл, откройте его и скопируйте его содержимое в **embedded** каталога.
    В Mac OS, распакуйте **.aar** файла с помощью **Распакуйте** команду в окне терминала (например, **Распакуйте file.aar**).

На этом этапе вы вручную установили отсутствующие компоненты, и ваш проект должен быть построен без ошибок. Если это не так, убедитесь, что вы скачали **m2repository** **ZIP-файл** архивировать версию, которая точно соответствует версии в сообщении об ошибке и убедитесь, что вы установили его содержимое в Исправьте расположениях, как описано в приведенных выше шагах. 



## <a name="summary"></a>Сводка 

В этой статье было рассмотрено, как восстановить из распространенных ошибок, которые можно выполнить во время автоматическую загрузку и установку зависимостей библиотек. Оно описано, как удалить проблемный библиотеку и перестройте проект, чтобы повторно загрузить и повторно установить библиотеку. Оно было описано, как загрузить библиотеку и установить его в **zips** папки. Он также описано сложнее процедура вручную загрузить и установить необходимые файлы как способ предотвращения проблем, которые не удается разрешить с помощью автоматического средства. 
