---
title: Файлы расширения APK
ms.prod: xamarin
ms.assetid: DB7E38E8-3C4E-5191-27EA-22DE63044FE2
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 38568fa9258c7e3de2c3333cdca5dc7d5867319c
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50117106"
---
# <a name="apk-expansion-files"></a>Файлы расширения APK

Для некоторых приложений (например, для некоторых игр) требуются дополнительные ресурсы и активы, которые могут быть предоставлены с учетом ограничения по максимальному размеру приложения Android, накладываемого Google Play. Это ограничение зависит от версии Android, для которой предназначен пакет APK:

-  100 МБ для пакетов APK, предназначенных для Android 4.0 или более поздних версий (API уровня 14 или выше);
-  50 МБ для пакетов APK, предназначенных для Android 3.2 или более ранних версий (API уровня 13 или выше).

Чтобы преодолеть это ограничение, Google Play будет размещать и распространять два *файла расширения* вместе с пакетом APK, позволяя приложению косвенно превышать этот предел. 

При установке приложения на большинстве устройств файлы расширения скачиваются вместе с пакетом APK и сохраняются в общее хранилище (на SD-карту или подключаемый по USB раздел) на устройстве. На некоторых устаревших устройствах файлы расширения файлов не могут автоматически устанавливаться с пакетом APK. В таких ситуациях приложение должно содержать код, который будет скачивать файлы расширения при первом запуске приложения пользователем.

Файлы расширения считаются *непрозрачными двоичными BLOB-объектами (obb)* и могут иметь размер до 2 ГБ. Android специально не обрабатывает эти скачанные файлы — они могут иметь любой подходящий для приложения формат. По существу, для файлов расширения действует следующий рекомендуемый подход:

-   **Расширение main** — это основной файл расширения для ресурсов, которые превышают ограничение размера пакета APK. Файл расширения main должен содержать основные ресурсы, необходимые приложению, и редко обновляться.
-   **Расширение patch** — это расширение предназначено для небольших обновлений файла расширения main. Этот файл можно обновлять. Применение необходимых исправлений или обновлений из этого файла входит в обязанности приложения.


Файлы расширения должны отправляться одновременно с пакетом APK.
Google Play не допускает отправку файла расширения в существующей пакет APK или обновление существующих пакетов APK. Если необходимо обновить файл расширения, следует отправить новый пакет APK с обновленным `versionCode`.


## <a name="expansion-file-storage"></a>Хранилище файлов расширения

Скачанные на устройство файлы хранятся в расположении **_shared-store_/Android/obb/_package-name_**:

-   **_shared-store_** — это каталог, заданный свойством `Android.OS.Environment.ExternalStorageDirectory`.
-   **_package-name_** — это имя пакета приложения в стиле Java.


Скачанные файлы расширения нельзя перемещать, изменять, переименовывать или удалять из их расположений на устройстве. В противном случае это приведет к повторному скачиванию файлов и удалению прежних файлов. Кроме того, каталог файлов расширения должен содержать только файлы пакета расширения.

Файлы расширения не обеспечивают безопасность или защиту своего содержимого — к файлам, сохраненным в общем хранилище, могут обращаться другие приложения или пользователи.

Если необходимо распаковать файл расширения, распакованные файлы следует сохранить в отдельном каталоге, например в указанном свойством `Android.OS.Environment.ExternalStorageDirectory`.

Альтернативой извлечению файлов из файла расширения является считывание ресурсов напрямую из файла расширения. Файл расширения — это не что иное, как ZIP-файл, который можно использовать с соответствующим `ContentProvider`. [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) содержит сборку [System.IO.Compression.Zip](https://github.com/mattleibow/Android.Play.ExpansionLibrary/tree/master/System.IO.Compression.Zip), в состав которой входит `ContentProvider`, предоставляющий прямой доступ к некоторым файлам мультимедиа. Если файлы мультимедиа упакованы в ZIP-файл, вызовы воспроизведения мультимедиа могут напрямую использовать файлы в ZIP-файле без распаковки архива. Добавляемые в ZIP-файл файлы мультимедиа не должны быть сжаты. 


### <a name="filename-format"></a>Формат имени файла

После скачивания файлов расширения Google Play будет использовать следующую схему для именования расширения:

    [main|patch].<expansion-version>.<package-name>.obb

В эту схему входят три компонента:

-   `main` или `patch` — указывает, является ли этот файл основным или файлом исправления. Допускается только один файл каждого типа.
-   `<expansion-version>` — это целое число, соответствующее `versionCode` пакета APK, с которым был связан файл.
-   `<package-name>` — это имя пакета приложения в стиле Java.


Например, если пакет APK имеет версию 21 и имя `mono.samples.helloworld`, имя основного файла расширения будет **main.21.mono.samples.helloworld**.


## <a name="download-process"></a>Процесс скачивания

При установке приложения из Google Play файлы расширения должны быть скачаны и сохранены вместе с пакетом APK. Однако в некоторых случаях это не происходит или файлы расширения могут быть удалены. Чтобы разобраться с этим условием, приложению необходимо проверить наличие файлов расширения, а затем скачать их, если это необходимо. На следующей блок-схеме показано рекомендуемое выполнение этого процесса:

[![Блок-схема файлов расширения APK](apk-expansion-files-images/apkexpansion.png)](apk-expansion-files-images/apkexpansion.png#lightbox)

При запуске приложение должно проверить наличие соответствующих файлов расширения на текущем устройстве. Если файлы отсутствуют, приложению следует сделать запрос из [службы лицензирования приложений](http://developer.android.com/google/play/licensing/index.html) Google Play. Эта проверка выполняется с помощью *библиотеки проверки лицензий (LVL)* и должна проводиться для бесплатных и лицензированных приложений. Библиотека LVL используется в основном в платных приложениях для применения ограничений, накладываемых лицензированием. Однако компания Google расширила возможности библиотеки, чтобы ее можно было также использовать с библиотеками расширения. Бесплатные приложения должны проверять библиотеку LVL, но могут игнорировать лицензионные ограничения. Запрос LVL отвечает за предоставление следующих сведений о файлах расширения, необходимых приложению: 

-   **Размер файла** — размеры файлов расширения используются в ходе проверки, которая определяет, скачаны ли уже правильные файлы расширения.
-   **Имена файлов** — это имя файла (на текущем устройстве) для сохранения пакетов расширения.
-   **URL-адрес для скачивания** — URL-адрес, который должен использоваться для скачивания пакетов расширения. Он уникален для каждого скачивания и прекращает действовать вскоре после предоставления.


После проверки LVL приложение должно скачать файлы расширения с учетом следующих моментов в ходе процесса скачивания:

-  На устройстве может быть недостаточно места для хранения файлов расширения.
-  Если сеть Wi-Fi недоступна, пользователю следует разрешить приостановить или отменить скачивание во избежание нежелательных расходов.
-  Файлы расширения скачиваются в фоновом режиме, чтобы не нарушать работу пользователя.
-  Во время скачивания в фоновом режиме должен отображаться индикатор хода выполнения.
-  Ошибки, возникающие во время скачивания, обрабатываются корректно.



## <a name="architectural-overview"></a>Общие сведения об архитектуре

При запуске основное действие проверяет, скачаны ли файлы расширения. Если это так, их необходимо проверить на допустимость.

Если файлы расширения не были скачаны или текущие файлы являются недопустимыми, следует скачать новые файлы расширения. Привязанная служба создается как часть приложения. Запущенное основное действие приложения использует привязанную службу для проверки служб лицензирования Google с целью определения имен файлов расширения и URL-адреса скачиваемых файлов. Затем привязанная служба скачает файлы в фоновом потоке.

Для оптимизации процесса интеграции файлов расширения в приложение компания Google создала несколько библиотек на Java. Эти библиотеки перечислены ниже:

-   **Библиотека средства скачивания** — библиотека, которая упрощает интеграцию файлов расширения в приложение. Она скачивает файлы расширения в фоновой службе, отображает уведомления для пользователей, обрабатывает проблемы с сетевым подключением, возобновляет скачивания и т. д.
-   **Библиотека проверки лицензий (LVL)** — библиотека для создания и обработки вызовов служб лицензирования приложений. Ее также можно использовать для проверок лицензирования, чтобы узнать, авторизовано ли приложение для работы на устройстве.
-   **Библиотека ZIP-файла расширения APK (необязательная.** — если файлы расширения упакованы в ZIP-файл, эта библиотека будет использоваться в качестве поставщика содержимого, позволяя приложению считывать ресурсы и активы непосредственно из ZIP-файла (без его распаковки).


Эти библиотеки были перенесены в C# и доступны по лицензии Apache 2.0. Чтобы быстро интегрировать файлы расширения в существующее приложение, можно добавить эти библиотеки в существующее приложение Xamarin.Android. Код доступен в [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary) на сайте GitHub.
