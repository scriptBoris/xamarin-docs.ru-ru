---
title: Сертификаты и идентификаторы в Xamarin.Mac
description: В этом руководстве описывается создание необходимых сертификатов и идентификаторов, которые потребуются для публикации приложения Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 393d0066-7f6f-4ac3-a48d-4b5db65bc4cd
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: b86f731009380db7e9e8980114272ddd38d2f0da
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34792879"
---
# <a name="certificates-and-identifiers-in-xamarinmac"></a>Сертификаты и идентификаторы в Xamarin.Mac

_В этом руководстве описывается создание необходимых сертификатов и идентификаторов, которые потребуются для публикации приложения Xamarin.Mac._

## <a name="certificates-and-identifiers"></a>Сертификаты и идентификаторы

Чтобы настроить компьютер Mac для разработки, посетите [Apple Developer Member Center](http://developer.apple.com). Главное меню показано ниже:

[![Центр участников разработки для Apple](certificates-identifiers-images/devcenter01.png "Центр участников разработки для Apple")](certificates-identifiers-images/devcenter01-large.png#lightbox)

Щелкните ссылку **Certificates, Identifiers & Profiles** (Сертификаты, идентификаторы и профили):

[![Выбор сертификатов, идентификаторов и профилей](certificates-identifiers-images/devcenter02.png "Выбор сертификатов, идентификаторов и профилей")](certificates-identifiers-images/devcenter02-large.png#lightbox)

Далее в разделе **Mac Apps** (Приложения Mac) щелкните ссылку **Certificates** (Сертификаты):

[![Выбор ссылки "Сертификаты"](certificates-identifiers-images/devcenter03.png "Выбор ссылки \"Сертификаты\"")](certificates-identifiers-images/devcenter03-large.png#lightbox)

Щелкните ссылку **All** (Все) и нажмите кнопку **+**:

[![Выбор всех элементов и добавление нового](certificates-identifiers-images/certif01.png "Выбор всех элементов и добавление нового")](certificates-identifiers-images/certif01-large.png#lightbox)

В открывшемся окне при необходимости скачайте **промежуточные сертификаты** (центры сертификации Worldwide Developer Relations и Developer ID). Однако эти сертификаты должны быть автоматически настроены средой Xcode.

Далее в этом разделе подробно описываются действия, которые необходимо выполнить в каждом из четырех разделов, чтобы завершить настройку учетной записи разработчика Mac.

-   **Register Mac App ID** (Регистрация идентификатора приложения Mac) — разработчику необходимо выполнить эти действия для каждого создаваемого приложения.
-   **Register macOS Systems** (Регистрация систем macOS) — требуется только при добавлении компьютеров для тестирования.
-   **Create Certificates** (Создание сертификатов) — требуется выполнить при настройке сертификатов и выполнять в дальнейшем при их продлении.
-   **Create Provisioning Profile** (Создание профиля подготовки) — разработчику необходимо выполнять эти действия для каждого создаваемого приложения, а также при добавлении новых систем.

Чтобы вернуться в это меню в любой момент, щелкните ссылку **Overview** (Обзор) в левом верхнем углу страницы.

### <a name="register-mac-app-id"></a>Регистрация идентификатора приложения Mac

Разработчику необходимо регистрировать идентификатор приложения для каждого создаваемого приложения. Чтобы создать запись для простейшего образца приложения MacWriter, выполните указанные ниже действия.

1. Введите **описание идентификатора приложения** и выберите любые **службы приложений**, которые потребуются приложению: 

    [![Ввод описания и служб приложения](certificates-identifiers-images/devcenter04.png "Ввод описания и служб приложения")](certificates-identifiers-images/devcenter04-large.png#lightbox)
2. Введите **идентификатор пакета** для приложения и нажмите кнопку **Continue** (Продолжить): 

    [![Ввод идентификатора пакета](certificates-identifiers-images/devcenter05.png "Ввод идентификатора пакета")](certificates-identifiers-images/devcenter05-large.png#lightbox)
3. Проверьте сведения и нажмите кнопку **Submit** (Отправить): 

    [![Проверка данных](certificates-identifiers-images/devcenter06.png "Проверка данных")](certificates-identifiers-images/devcenter06-large.png#lightbox)

Некоторые **службы приложений** могут требовать дальнейшей настройки (например, iCloud). В этом случае выберите только что созданный идентификатор приложения и нажмите кнопку **Edit** (Изменить):

[![Изменение идентификатора для нового приложения](certificates-identifiers-images/devcenter07.png "Изменение идентификатора для нового приложения")](certificates-identifiers-images/devcenter07-large.png#lightbox)

Чтобы настроить службы iCloud, нажмите кнопку **Edit** (Изменить):

[![Настройка служб iCloud](certificates-identifiers-images/devcenter08.png "Настройка служб iCloud")](certificates-identifiers-images/devcenter08-large.png#lightbox)

В этом окне разработчик может настроить базы данных, которые будут использоваться:

[![Настройка баз данных](certificates-identifiers-images/devcenter09.png "Настройка баз данных")](certificates-identifiers-images/devcenter09-large.png#lightbox)

### <a name="register-macos-systems"></a>Регистрация систем macOS

Чтобы создать профиль подготовки для тестирования, разработчику необходимо зарегистрировать свои компьютеры Mac. Для тестирования приложений Mac он может зарегистрировать до 100 компьютеров.

В центре разработчика Mac щелкните ссылку **All** (Все) в разделе **Devices** (Устройства) и нажмите кнопку **+**:

[![Добавление нового компьютера](certificates-identifiers-images/devcenter10.png "Добавление нового компьютера")](certificates-identifiers-images/devcenter10-large.png#lightbox)

Введите **имя** и идентификатор **UUID** добавляемого компьютера, а затем нажмите кнопку **Continue** (Продолжить). Проверьте сведения и нажмите кнопку **Register** (Зарегистрировать):

[![Ввод сведений о новом компьютере](certificates-identifiers-images/devcenter11.png "Ввод сведений о новом компьютере")](certificates-identifiers-images/devcenter11-large.png#lightbox)

### <a name="create-certificates"></a>Создание сертификатов

В разделе Certificates (Сертификаты) можно создать несколько разных типов сертификатов, которые будут использоваться для подписывания приложений Mac:

[![Создание нового сертификата](certificates-identifiers-images/certif01.png "Создание нового сертификата")](certificates-identifiers-images/certif01-large.png#lightbox)

Имеются три основных типа сертификатов:

-   **Сертификат разработки Mac** — необязателен в общем случае, но требуется, если разработчик планирует использовать такие функции, как iCloud или push-уведомления. Сертификат разработки необходим для создания профилей подготовки, которые предоставляют доступ к этим функциям.
-   **Mac App Store** — разработчику потребуется один такой сертификат для приложения и еще один — для установщика.
-   **Идентификатор разработчика** — требуются сертификаты для приложения и установщика в случае распространения не через Mac App Store.

В следующих разделах приводятся примеры создания сертификатов каждого из перечисленных выше типов.

#### <a name="mac-development-certificate"></a>Сертификат разработки Mac

Как было сказано ранее, сертификат разработки приложения Mac требуется только в том случае, если используются такие функции macOS, как iCloud или push-уведомления.

Чтобы создать сертификат разработки, выполните указанные ниже действия:

1. Установите переключатель в положение **Mac Development** (Разработка Mac) и нажмите кнопку **Continue** (Продолжить): 

     [![Добавление сертификата разработки](certificates-identifiers-images/certif02.png "Добавление сертификата разработки")](certificates-identifiers-images/certif02-large.png#lightbox)
2. На следующем экране описывается, как с помощью функции доступа к связке ключей создать файл запроса на подписывание сертификата для отправки: 

    [![Экран передачи доступа к цепочке ключей](certificates-identifiers-images/certif03.png "Экран передачи доступа к цепочке ключей")](certificates-identifiers-images/certif03-large.png#lightbox)
3. Выберите осмысленное общее имя для сертификата, по которому его легко можно будет определить после создания. Запомните место сохранения файла, чтобы его можно было найти в следующем шаге: 

     ![Экспорт сертификата](certificates-identifiers-images/image12.png "Экспорт сертификата")
4. Файл запроса сертификата (с расширением `.certSigningRequest`) будет сохранен на локальном компьютере Mac. Запомните место его сохранения (по умолчанию это рабочий стол), так как его потребуется выбрать в следующем шаге: 

     [![Отправка файла сертификата](certificates-identifiers-images/image13.png "Отправка файла сертификата")](certificates-identifiers-images/image13-large.png#lightbox)
5. Нажмите кнопку **Download** (Скачать), чтобы получить сертификат, и дважды щелкните его, чтобы установить его в **связке ключей**: 

     [![Загрузка сертификата разработки](certificates-identifiers-images/image15.png "Загрузка сертификата разработки")](certificates-identifiers-images/image15-large.png#lightbox)
6. Нажмите кнопку **Download** (Скачать), чтобы получить сертификат, и дважды щелкните его, чтобы установить его в **связке ключей**. В **служебной программе для сертификата разработчика** сертификаты отображаются следующим образом: 

     [![Служебная программа для сертификата разработчика](certificates-identifiers-images/image16.png "Служебная программа для сертификата разработчика")](certificates-identifiers-images/image16-large.png#lightbox)
7. Он также отображается в **связке ключей** следующим образом: 

     ![Сертификат в цепочке ключей](certificates-identifiers-images/image17.png "Сертификат в цепочке ключей")

Как упоминалось ранее, сертификат разработчика требуется не всегда, а только если разработчик внедряет такие функции macOS, как iCloud и push-уведомления. Он также необходим для создания **профиля подготовки к разработке**, который требуется для тестирования приложений Mac App Store.

#### <a name="mac-app-store-certificates"></a>Сертификаты Mac App Store

Для выпуска приложения в App Store необходимо создать сертификат **Mac App Store**, с помощью которого будут подписываться приложение и пакет установщика Mac.

1. В качестве типа сертификата выберите **Mac App Store** и нажмите кнопку **Continue** (Продолжить): 

    [![Создание сертификата App Store](certificates-identifiers-images/certif04.png "Создание сертификата App Store")](certificates-identifiers-images/certif04-large.png#lightbox)
2. Выберите тип создаваемого сертификата (для выпуска в App Store потребуется по одному сертификату каждого типа): 

    [![Выбор типа сертификата](certificates-identifiers-images/certif05.png "Выбор типа сертификата")](certificates-identifiers-images/certif05-large.png#lightbox)
3. На следующей странице объясняется, как использовать **доступ к связке ключей** для создания файла запроса сертификата. Следуйте инструкциям: 

     [![Создание запроса на цепочку ключей](certificates-identifiers-images/certif06.png "Создание запроса на цепочку ключей")](certificates-identifiers-images/certif06-large.png#lightbox)
4. Выберите описательное **общее имя**, например, используйте в имени текст "Приложение App Store". 

     ![Ввод описательного имени](certificates-identifiers-images/image20.png "Ввод описательного имени")
5. Файл запроса сертификата (с расширением `.certSigningRequest`) будет сохранен на локальном компьютере Mac. Запомните место его сохранения (по умолчанию это рабочий стол): 

     [![Сохранение сертификата](certificates-identifiers-images/image21.png "Сохранение сертификата")](certificates-identifiers-images/image21-large.png#lightbox)
6. Нажмите кнопку **Download** (Скачать), чтобы получить сертификат, и дважды щелкните его, чтобы установить его в **связке ключей**: 

      [![Скачивание сертификата App Store](certificates-identifiers-images/image23.png "Скачивание сертификата App Store")](certificates-identifiers-images/image23-large.png#lightbox)
7. Нажмите кнопку **Continue** (Продолжить) и выполните те же самые действия, чтобы скачать еще один сертификат, на этот раз для *установщика*: 

     [![Выбор установщика](certificates-identifiers-images/image24.png "Выбор установщика")](certificates-identifiers-images/image24-large.png#lightbox)
8. Выберите описательное **общее имя**, например, используйте в имени текст "Установщик App Store": 

     ![Настройка имени сертификата](certificates-identifiers-images/image25.png "Настройка имени сертификата")
9. Файл запроса сертификата (с расширением `.certSigningRequest`) будет сохранен на локальном компьютере Mac. Запомните место его сохранения (по умолчанию это рабочий стол): 

     [![Отправка сертификата](certificates-identifiers-images/image26.png "Отправка сертификата")](certificates-identifiers-images/image26-large.png#lightbox) 

     [![Скачивание сертификата распространения](certificates-identifiers-images/image28.png "Скачивание сертификата распространения")](certificates-identifiers-images/image28-large.png#lightbox)
10. Нажмите кнопку **Download** (Скачать), чтобы получить сертификат, и дважды щелкните его, чтобы установить его в **связке ключей**. В служебной программе для сертификата разработчика сертификаты отображаются следующим образом: 

     [![Служебная программа для сертификата разработчика](certificates-identifiers-images/image29.png "Служебная программа для сертификата разработчика")](certificates-identifiers-images/image29-large.png#lightbox)
11. В **связке ключей** теперь будут отображаться два новых сертификата: 

     [![Сертификат в цепочке ключей](certificates-identifiers-images/image30.png "Сертификат в цепочке ключей")](certificates-identifiers-images/image30-large.png#lightbox)

#### <a name="developer-id-certificates"></a>Сертификаты идентификации разработчиков

Для самостоятельного выпуска приложения Xamarin.Mac (не через Apple App Store) разработчику потребуется сертификат идентификатора разработчика, с помощью которого приложение будет подписываться для выпуска и установки.

Выполните следующие действия:

1. В разделе **Certificates** (Сертификаты) сначала нажмите кнопку **+**, а затем установите переключатель в положение **Developer ID** (Идентификатор разработчика): 

    [![Добавление идентификатора разработчика](certificates-identifiers-images/certif07.png "Добавление идентификатора разработчика")](certificates-identifiers-images/certif07-large.png#lightbox)
2. Нажмите кнопку **Continue** (Продолжить) и выберите тип создаваемого идентификатора разработчика: 

    [![Выбор типа для идентификатора разработчика](certificates-identifiers-images/certif08.png "Выбор типа для идентификатора разработчика")](certificates-identifiers-images/certif08-large.png#lightbox)
3. Потребуются два сертификата: один для подписывания самого приложения, а другой для подписывания его установщика. При присвоении имен запросам сертификатов используйте описательные имена, содержащие текст `Application` и `Installer`, чтобы их можно было определить позднее.
4. На следующем экране приводятся подробные указания по созданию сертификата. Нажмите кнопку **Continue** (Продолжить). 

    [![Создание сертификата](certificates-identifiers-images/certif09.png "Создание сертификата")](certificates-identifiers-images/certif09-large.png#lightbox)
5. Выберите описательное **общее имя**, например, используйте в имени текст "Идентификатор разработчика приложения": 

     ![Ввод имени сертификата](certificates-identifiers-images/image33.png "Ввод имени сертификата")
6. Файл запроса сертификата (с расширением `.certSigningRequest`) будет сохранен на локальном компьютере Mac. Запомните место его сохранения (по умолчанию это рабочий стол): 

     [![Отправка сертификата](certificates-identifiers-images/certif10.png "Отправка сертификата")](certificates-identifiers-images/certif10-large.png#lightbox) 

     [![Скачивание идентификатора разработчика](certificates-identifiers-images/certif11.png "Скачивание идентификатора разработчика")](certificates-identifiers-images/certif11-large.png#lightbox)
7. Нажмите кнопку **Download** (Скачать), чтобы получить сертификат, и дважды щелкните его, чтобы установить его в **связке ключей**.
8. Нажмите кнопку **Continue** (Продолжить) и выполните те же самые действия, чтобы скачать еще один сертификат, на этот раз для *установщика*.
9. Выберите описательное **общее имя**, например, используйте в имени текст "Идентификатор разработчика для установщика": 

     ![Ввод общего имени](certificates-identifiers-images/image38.png "Ввод общего имени")
10. Файл запроса сертификата (с расширением `.certSigningRequest`) будет сохранен на локальном компьютере Mac. Запомните место его сохранения (по умолчанию это рабочий стол): 

     [![Отправка сертификата](certificates-identifiers-images/certif10.png "Отправка сертификата")](certificates-identifiers-images/certif10-large.png#lightbox)
11. Сертификат станет доступен для скачивания. Нажмите кнопку **Download** (Скачать), прежде чем нажимать кнопку **Done** (Готово): 

     [![Скачивание сертификата](certificates-identifiers-images/certif11.png "Скачивание сертификата")](certificates-identifiers-images/certif11-large.png#lightbox)
12. Нажмите кнопку **Download** (Скачать), чтобы получить сертификат, и дважды щелкните его, чтобы установить его в **связке ключей**. В **служебной программе для сертификата разработчика** сертификаты отображаются следующим образом: 

     [![Служебная программа для сертификата разработчика](certificates-identifiers-images/certif12.png "Служебная программа для сертификата разработчика")](certificates-identifiers-images/certif12-large.png#lightbox)
13. В **связке ключей** будут видны следующие элементы: 

     [![Сертификат в цепочке ключей](certificates-identifiers-images/image43.png "Сертификат в цепочке ключей")](certificates-identifiers-images/image43-large.png#lightbox)


## <a name="related-links"></a>Связанные ссылки

- [Установка](/visualstudio/mac/installation/)
- [Пример кода приложения "Привет, Mac"](~/mac/get-started/hello-mac.md)
- [Распространение приложений в Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Идентификатор разработчика и привратник](https://developer.apple.com/resources/developer-id/)
